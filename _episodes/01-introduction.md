---
title: "Introduction"
teaching: 15
exercises: 0
questions:
- "FIXME?"
objectives:
- "FIXME"
keypoints:
- "FIXED ME"
---

Like we noted in the Setup section, our goal in this tutorial is to understand how to use
the PODIO interface in an analysis.  As such, this tutorial will build on the earlier
[Analysis Tutorial][analysis], which discussed how to analyze our reconstruction output with
the ROOT [TTreeReader][reader] and [RDataFrame][dataframe].

Our vehicle for doing so will be two commmon and complementary analysis routines:

1. Matching tracks and clusters; and
2. Matching decay products to their parents.

On one hand, the former utilizes purely reconstructed objects (tracks and clusters) and will
illustrate the basics of PODIO and _links_.  While on the other hand, the latter utilizes purely
simulated objects (generated particles) and will illustrate how to navigate _relations_.

## Why these?

Before diving into PODIO, though, I would like to motivate _why_ you should care about these
two routines.  To start, remember that a particle is _not_ just a track, a cluster, or so
on.  A particle will leave signals in several detectors.  The majority of particles in a
typical DIS event will create hits in our trackers _and_ will shower &mdash forming a cluster
&mdash in at least one of our calorimeters.

This is illustrated in the following image, where the colored bands indicate the SVT (orange),
hpDIRC (pink), BIC (purple), magnet solenoid (grey), and BHCal (blue) in ePIC's barrel. 

![Illustration of a particle leaving signals in several detectors](../../assets/img/tutorial/ParticleTrajectory.png)

WE NEED TO USE ALL INFORMATION TO GET THE MOST ACCURATE MEASUREMENT. TO BALANCE ENERGY AND DO PID.

![Diagram of particle types vs. typical survival depth in a detector](../../assets/img/tutorial/DepthVsParticleSpecies.png)

ELECTRONS SHOWER LEAVE HITS IN THE TRACKER AND ONLY SHOWER IN ECAL, BUT CHARGED HADRONS CAN LEAVE SIGNALS IN BOTH.

## References

[analysis]: https://eic.github.io/tutorial-analysis
[reader]: https://root.cern.ch/doc/master/classTTreeReader.html
[dataframe]: https://root.cern/doc/master/classROOT_1_1RDataFrame.html

## Outline

Key points:
  - Illustrate basics of PODIO usage in
    C++, Python
  - Illustrate navigating links, associations,
    and relations

Skeleton:

1. Introduction
    - Goal of tutorial
    - Track-cluster Matching: illustrate PODIO
      basics + links
      - Paradigmatic of a lot of analysis operations
      - First step towards particle/jet/event
        reconstruction
      - e.g. reconstructing a J/psi, you need
        to ID electrons
    - Parent-Daughter Matching: illustrate
      navigating relations
      - Truth-level parallel of track-cluster
        matching
      - e.g. need to validate our reconstructed
        J/psi

{% include links.md %}
