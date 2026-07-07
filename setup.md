---
title: Setup
---

If you have not done so already, please follow the instructions [here](https://eic.github.io/tutorial-setting-up-environment/setup.html)
well before the start of the tutorial to ensure your system is ready. You will need a working `eic-shell` environment running.


This tutorial will cover the basics of [PODIO](https://github.com/AIDASoft/podio) and illustrate how to use it in an analysis setting
through the lens of a critical operation in our reconstruction, matching tracks to clusters. We will also touch on other examples of how
to use PODIO in your analysis such as identifying the decay electrons of a $J$/$\psi$. Examples of how to do the same analyses in other
formats --- such as with `RDataFrame` or with `TTreeReader` --- are included as extras at the end of the tutorial.

We will need a file to process for the example code, so if you havent done so yet, please follow the tutorial on accessing EIC/ePIC
simulation data with Rucio [here](https://eic.github.io/tutorial-file-access/). We suggest using a high-$Q^{2}$ DIS file since most
events will have many particles in them. For example, you can run the following command in the `eic-shell`:

```
xrdcp root://dtn-eic.jlab.org//volatile/eic/EPIC/RECO/26.03.0/epic_craterlake/DIS/NC/10x100/minQ2=100/pythia8NCDIS_10x100_minQ2=100_beamEffects_xAngle=-0.
025_hiDiv_5.0001.eicrecon.edm4eic.root ./
```

**Note:** might be preferable to use exclusive channel (w/ or w/o background) where there will be a clear signal, e.g.
Lambda --> pi+proton, Ks --> pi+pi

**Note:** exclusive J/psi --> ee enchanced channel

```
epic:/RECO/26.04.1/epic_craterlake/EXCLUSIVE/DIFFRACTIVE_JPSI_ABCONV/lAger3.6.1-1.0/10x130/hiAcc
```

Lastly, you may want to work through some sections of the introductory analysis tutorial [here](https://eic.github.io/tutorial-analysis/).
In particular, section [2](https://eic.github.io/tutorial-analysis/02-reconstruction-output/index.html) touches on the structure of
the ROOT trees we'll be analyzing.

{% include links.md %}
