---
title: "Track-Cluster Matching"
teaching: 10
exercises: 35
questions:
- "How do actually I use PODIO?"
objectives:
- "Use PODIO interface to find reconstructed J/psi"
keypoints:
- "We can match tracks to clusters based on distances in eta-phi space"
- "Care is needed to make sure you're picking out the relevant cluster"
- "Electrons can be identified with an E/p cut"
- "J/psi can be reconstructed by considering pairs of reconstructed electrons"
---

## Exercise Goal

Now that you have some background, let's get some hands-on experience with
PODIO.  Our goal in this epsiode will be to see if we can reconstruct $J/psi$ .
The next episode will then have us compare these to truth/MC objects.

We'll build an analysis script/macro up bit-by-bit.  But if you're having
trouble placing the code snippets below, you can always refer to the
[full example scripts](https://github.com/eic/tutorial-track-cluster-matching/blob/gh-pages/_extras/full-example-script.md)
for guidance.

## Step 0: Skeleton

Overall, our strategy will proceed in two steps:

1. Match tracks to EMCal clusters and identify electrons with an E/p cut; and then
2. Form pairs of electrons and identify J/psi with an invariant mass cut.

The code below will illustrate how to do this using (1) Python and ROOT, and (2)
C++ and ROOT (both cases using PODIO).  In the future, we'll expand this tutorial
with some extra documents illustrating how to do the same analysis using alternative
analysis techniques.

So to begin, let's 1st create a file.  We will suppose the file is called `FindJPsi.py`
 (for the Python version) or `FindJPsi.cxx` (for the C++ version), but you are ---
of course --- free to name the file what you want.

> ## `Exercise: Skeleton`
> Now, in that file, let's create a skeleton.  For Python:
> ```python
> import argparse as ap
> import ROOT
> import numpy as np
>
> from podio.reading import get_reader
>
> # Default options
> in_file_def   = "lAger3.6.1-1.0_jpsi_10x130_hiAcc_run1.0009.eicrecon.edm4eic.root"
> out_file_def  = "jpsi_search_py.analysis.root"
> match_cut_def = 0.2
> ep_min_def    = 0.8
> ep_max_def    = 1.2
> mass_min_def  = 2.5
> mass_max_def  = 3.5
>
>
> def FindJPsi(
>     in_file   = in_file_def, 
>     out_file  = out_file_def,
>     match_cut = match_cut_def,
>     ep_min    = ep_min_def,
>     ep_max    = ep_max_def,
>     mass_min  = mass_min_def,
>     mass_max  = mass_max_def,
> ):
>
>     # Open reader and output file ============================================
>
>     # use podio reader to open example EICrecon output
>     reader = get_reader(in_file)
>
>
>     # Event loop ==============================================================
>
>     for frame in reader.get("events"):
>
>         # Grab all the collections we'll need
>         particles    = frame.get("MCParticles");
>         tracks       = frame.get("CentralCKFTracks");
>         clusters_neg = frame.get("EcalEndcapNClusters");
>         clusters_cen = frame.get("EcalBarrelClusters");
>         clusters_pos = frame.get("EcalEndcapPClusters");
>         associations = frame.get("CentralCKFTrackAssociations");
>
>
> # Main ========================================================================
>
> if __name__ == "__main__":
>
>     # set up argments
>     parser = ap.ArgumentParser()
>     parser.add_argument("--infile", default = in_file_def)
>     parser.add_argument("--outfile", default = out_file_def)
>     parser.add_argument("--matchcut", default = match_cut_def)
>     parser.add_argument("--epmin", default = ep_min_def)
>     parser.add_argument("--epmax", default = ep_max_def)
>     parser.add_argument("--massmin", default = mass_min_def)
>     parser.add_argument("--massmax", default = mass_max_def)
>     args = parser.parse_args()
>
>     # Run calculation
>     FindJPsi(
>         args.infile,
>         args.outfile,
>         args.matchcut,
>         args.epmin,
>         args.epmax,
>         args.massmin,
>         args.massmax,
>     )
>
> ```
>
> And for C++:
>
> ```c++
> #include <edm4eic/ClusterCollection.h>
> #include <edm4eic/MCRecoTrackParticleAssociationCollection.h>
> #include <edm4eic/TrackCollection.h>
> #include <edm4hep/MCParticleCollection.h>
> #include <edm4hep/Vector3f.h>
> #include <podio/Frame.h>
> #include <podio/ObjectID.h>
> #include <podio/ROOTReader.h>
> #include <Math/Vector3D.h>
> #include <Math/Vector4D.h>
> #include <TFile.h>
> #include <TH1.h>
> #include <cmath>
> #include <optional>
> #include <map>
> #include <memory>
> #include <set>
> #include <string>
> #include <vector>
> #include <utility>
>
>
> void FindJPsi(
>   const std::string& in_file   = "lAger3.6.1-1.0_jpsi_10x130_hiAcc_run1.0009.eicrecon.edm4eic.root",
>   const std::string& out_file  = "jpsi_search_cxx.analysis.root",
>   const float        match_cut = 0.2,
>   const float        ep_min    = 0.8,
>   const float        ep_max    = 1.2,
>   const float        mass_min  = 2.5,
>   const float        mass_max  = 3.5
> ) {
>
>   // Open reader and output file ==============================================
>
>   podio::ROOTReader reader = podio::ROOTReader();
>   reader.openFile(in_file);
>
>   TFile* output = new TFile(out_file.data(), "recreate");
>
>   // Event loop ===============================================================
>
>   while (auto entry = reader.readNextEntry(podio::Category::Event)) {
>
>     auto frame = podio::Frame(std::move(entry));
>
>     // Grab all the collections we'll need
>     auto& particles    = frame.get<edm4hep::MCParticleCollection>("MCParticles");
>     auto& tracks       = frame.get<edm4eic::TrackCollection>("CentralCKFTracks");
>     auto& clusters_neg = frame.get<edm4eic::ClusterCollection>("EcalEndcapNClusters");
>     auto& clusters_cen = frame.get<edm4eic::ClusterCollection>("EcalBarrelClusters");
>     auto& clusters_pos = frame.get<edm4eic::ClusterCollection>("EcalEndcapPClusters");
>     auto& associations = frame.get<edm4eic::MCRecoTrackParticleAssociationCollection>("CentralCKFTrackAssociations");
>
>   }  // end event loop
>
>   // Calculate efficiencies and save output ===================================
>
>   output->cd();
>   output->Write();
>   output->Close();
>   return;
>
> }
> ```
>
> Now, just to make sure there are no typos, try running your script with either
>
> ```bash
> python FindJPsi.py
> ```
>
> for Python. Or:
>
> ```bash
> root -b -q FindJPsi.cxx
> ```
>
> for C++.
{: .challenge}

During the event loop, notice how we 1st grab a `frame` and then grab all of
the relevant collections we need from it.  Like we mentioned in the previous
episode, a PODIO `Frame` aggregates all of the data for all collections in
a given category.

In this context, we can think of a single frame as an MC event.  In our actual
data-taking operations, a frame might be a "timeframe" of streamed data.

Lastly, a couple notes:
- For thsoe following along in Python who haven't seen this before, the chunk at
  the bottom under `if __name__ == "__main__":` is there just make it easy to
  specify command line arguments.
- You might also see how the options in both cases correspond to our 2 steps up
  above.  We'll see them in action soon.

Next, let's fill in our histograms.  We'll define all the ones we need for this
tutorial.  However, we strongly encoourage you to play around and add additional
ones!

> ## `Exercise:` Histograms
> Add the following code snippets somewhere near the top of your script/macro.
> For example right after you open the PODIO reader or right after you create
> your output file.
>
> ```python
>     # Create histograms ======================================================
>
>     h_track_momentum_all     = ROOT.TH1D("h_track_momentum_all", "Track #it{p} (all)", 50, 0.0, 10.0)
>     h_track_momentum_match   = ROOT.TH1D("h_track_momentum_match", "Track #it{p} (matched to cluster)", 50, 0.0, 10.0)
>     h_track_eta_all          = ROOT.TH1D("h_track_eta_all", "Track #eta (all)", 250, -5.0, 5.0)
>     h_track_eta_match        = ROOT.TH1D("h_track_eta_match", "Track #eta (matched to cluster)", 250, -5.0, 5.0)
>     h_track_cluster_eop      = ROOT.TH1D("h_track_cluster_eop", "Track-cluster E/p", 40, 0.0, 2.0)
>     h_invariant_mass         = ROOT.TH1D("h_invariant_mass", "Reconstructed invariant mass", 50, 0.0, 5.0)
>     h_daughter_momentum_all  = ROOT.TH1D("h_daughter_momentum_all", "Daughter e^{#pm} #it{p} (all)", 50, 0.0, 10.0)
>     h_daughter_momentum_reco = ROOT.TH1D("h_daughter_momentum_reco", "Daughter e^{#pm} #it{p} (reconstructed)", 50, 0.0, 10.0)
>     h_daughter_eta_all       = ROOT.TH1D("h_daughter_eta_all", "Daughter e^{#pm} #eta (all)", 250, -5.0, 5.0)
>     h_daughter_eta_reco      = ROOT.TH1D("h_daughter_eta_reco", "Daughter e^{#pm} #eta (reconstructed)", 250, -5.0, 5.0)
>     h_jpsi_momentum_all      = ROOT.TH1D("h_jpsi_momentum_all", "J/#psi #it{p} (all e^{#pm} decays)", 50, 0.0, 10.0)
>     h_jpsi_momentum_reco     = ROOT.TH1D("h_jpsi_momentum_reco", "J/#psi #it{p} (reconstructed e^{#pm} decays)", 50, 0.0, 10.0)
>     h_jpsi_eta_all           = ROOT.TH1D("h_jpsi_eta_all", "J/#psi #eta (all e^{#pm} decays)", 250, -5.0, 5.0)
>     h_jpsi_eta_reco          = ROOT.TH1D("h_jpsi_eta_reco", "J/#psi #eta (reconstructed e^{#pm} decays)", 250, -5.0, 5.0)
> ```
>
> ```c++
>   // Create histograms ========================================================
>
>   TH1D* h_track_momentum_all     = new TH1D("h_track_momentum_all", "Track #it{p} (all)", 50, 0.0, 10.0);
>   TH1D* h_track_momentum_match   = new TH1D("h_track_momentum_match", "Track #it{p} (matched to cluster)", 50, 0.0, 10.0);
>   TH1D* h_track_eta_all          = new TH1D("h_track_eta_all", "Track #eta (all)", 250, -5.0, 5.0);
>   TH1D* h_track_eta_match        = new TH1D("h_track_eta_match", "Track #eta (matched to cluster)", 250, -5.0, 5.0);
>   TH1D* h_track_cluster_eop      = new TH1D("h_track_cluster_eop", "Track-cluster E/p", 40, 0.0, 2.0);
>   TH1D* h_invariant_mass         = new TH1D("h_invariant_mass", "Reconstructed invariant mass", 50, 0.0, 5.0);
>   TH1D* h_daughter_momentum_all  = new TH1D("h_daughter_momentum_all", "Daughter e^{#pm} #it{p} (all)", 50, 0.0, 10.0);
>   TH1D* h_daughter_momentum_reco = new TH1D("h_daughter_momentum_reco", "Daughter e^{#pm} #it{p} (reconstructed)", 50, 0.0, 10.0);
>   TH1D* h_daughter_eta_all       = new TH1D("h_daughter_eta_all", "Daughter e^{#pm} #eta (all)", 250, -5.0, 5.0);
>   TH1D* h_daughter_eta_reco      = new TH1D("h_daughter_eta_reco", "Daughter e^{#pm} #eta (reconstructed)", 250, -5.0, 5.0);
>   TH1D* h_jpsi_momentum_all      = new TH1D("h_jpsi_momentum_all", "J/#psi #it{p} (all e^{#pm} decays)", 50, 0.0, 10.0);
>   TH1D* h_jpsi_momentum_reco     = new TH1D("h_jpsi_momentum_reco", "J/#psi #it{p} (reconstructed e^{#pm} decays)", 50, 0.0, 10.0);
>   TH1D* h_jpsi_eta_all           = new TH1D("h_jpsi_eta_all", "J/#psi #eta (all e^{#pm} decays)", 250, -5.0, 5.0);
>   TH1D* h_jpsi_eta_reco          = new TH1D("h_jpsi_eta_reco", "J/#psi #eta (reconstructed e^{#pm} decays)", 250, -5.0, 5.0);
> ```
>
> And for those using Python, add the following at the very end of your
> `FindJPsi()` function:
>
> ```python
>
>     with ROOT.TFile(out_file, "recreate") as ofile:
>         ofile.WriteObject(h_track_momentum_all)
>         ofile.WriteObject(h_track_momentum_match)
>         ofile.WriteObject(h_track_eta_all)
>         ofile.WriteObject(h_track_eta_match)
>         ofile.WriteObject(h_track_cluster_eop)
>         ofile.WriteObject(h_invariant_mass)
>         ofile.WriteObject(h_daughter_momentum_all)
>         ofile.WriteObject(h_daughter_momentum_reco)
>         ofile.WriteObject(h_daughter_eta_all)
>         ofile.WriteObject(h_daughter_eta_reco)
>         ofile.WriteObject(h_jpsi_momentum_all)
>         ofile.WriteObject(h_jpsi_momentum_reco)
>         ofile.WriteObject(h_jpsi_eta_all)
>         ofile.WriteObject(h_jpsi_eta_reco)
> ``` 
>
> Then run your script/macro to make sure there are no typos.
{: .challenge}

## Step 1: Find Electrons

TODO

## Step 2: Find J/Psi

TODO

## Outline

Key points:
  - Illustrate basics of PODIO usage in
    C++, Python
  - Illustrate navigating links, associations,
    and relations

Skeleton:

3. Track-cluster matching
    - (A) Set up basic script
      - Show track-cluster matching efficiency 
    - (B) Select e+- based on E/p
    - (C) Plot invariant mass
    - Note: have algorithm to do this in EICrecon,
      can make use of premade branches

{% include links.md %}
