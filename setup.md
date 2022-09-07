---
title: Setup
---

# For the tutorial

This tutorial uses a sampler of [Scikit-HEP project](https://scikit-hep.org/) packages (Uproot, Awkward Array, hist, Vector, zfit, Particle, fastjet), which are all components that you might or might not use in your analysis, as well as Python 3, NumPy, and a smattering of other mainstream libraries (Pandas, Matplotlib, JupyterLab, Numba).

Rather than asking you to install them all, just use this

<p align="center">
  <a href="https://mybinder.org/v2/gh/hsf-training/hsf-training-scikit-hep-webpage/main?urlpath=lab" target="_blank">
    <img src="https://mybinder.org/badge_logo.svg" alt="Launch Binder" width="200">
  </a>
</p>

to get a JupyterLab session with everything included. Once JupyterLab opens, click the "Python 3 (ipykernel)" button to get a new notebook.

# After the tutorial

If you want to install some of these packages on your own or your lab's computer, I recommend [Miniforge](https://github.com/conda-forge/miniforge) (or Anaconda/Miniconda with the [conda-forge channel](https://conda-forge.org/docs/user/introduction.html#how-can-i-install-packages-from-conda-forge)). This method also provides a way to [install ROOT in the same environment](https://github.com/conda-forge/root-feedstock#readme). After setting that up, Uproot (for instance) could be installed as

~~~
conda install numpy uproot4 awkward hist particle hepunits matplotlib boost-histogram iminuit zfit vector fastjet
~~~
{: .language-bash}

If one of the above-mentioned packages is not available on conda-forge (yet), it can be installed in the same environment using pip:

~~~
pip install numpy
pip install awkward
pip install uproot4
pip install scikit-hep-testdata
pip install hist
pip install particle
pip install hepunits
pip install matplotlib
pip install boost-histogram
pip install iminuit
pip install zfit
pip install vector
pip install fastjet
~~~
{: .language-bash}

Alternatively, you could install everything using pip only. This is the default way to install Python packages, and there are many tutorials, including [Python's own](https://packaging.python.org/en/latest/tutorials/installing-packages/).

{% include links.md %}
