---
title: Setup
---

# For the tutorial

This tutorial uses a sampler of [Scikit-HEP project](https://scikit-hep.org/) packages (Uproot, Awkward Array, hist, Vector, zfit, Particle, fastjet), which are all components that you might or might not use in your analysis, as well as Python 3, NumPy, and a smattering of other mainstream libraries (Pandas, Matplotlib, JupyterLab, Numba).

Rather than asking you to install them all, just use one of these buttons

<p align="center">
  <a href="https://codespaces.new/wdconinc/hsf-training-scikit-hep-webpage?quickstart=1" target="_blank">
    <img src="https://github.com/codespaces/badge.svg" alt="Launch GitHub Codespaces">
  </a>
</p>

or

<p align="center">
  <a href="https://mybinder.org/v2/gh/hsf-training/hsf-training-scikit-hep-webpage/main?urlpath=lab" target="_blank">
    <img src="https://mybinder.org/badge_logo.svg" alt="Launch Binder">
  </a>
</p>

to get a JupyterLab session with everything included. Once JupyterLab opens, click the "Python 3 (ipykernel)" button to get a new notebook.

# After the tutorial

If you want to install some of these packages on your own or your lab's computer, I recommend [Miniforge](https://github.com/conda-forge/miniforge) (or Anaconda/Miniconda with the [conda-forge channel](https://conda-forge.org/docs/user/introduction.html#how-can-i-install-packages-from-conda-forge)). This method also provides a way to [install ROOT in the same environment](https://github.com/conda-forge/root-feedstock#readme). To setup the environment use the environment.yml file in base of this repository as:


```bash
conda env create --file environment.yml
```

Alternatively, you can install all required packages locally with pip: Take a look at environment.yml for a list (this includes all dependencies listed under the pip key and some of the dependencies listed above).


{% include links.md %}
