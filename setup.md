---
title: Setup
---

# For the tutorial

This tutorial uses a sampler of [Scikit-HEP project](https://scikit-hep.org/) packages (Uproot, Awkward Array, hist, Vector, zfit, Particle, fastjet), which are all components that you might or might not use in your analysis, as well as Python 3, NumPy, and a smattering of other mainstream libraries (Pandas, Matplotlib, JupyterLab, Numba).

Rather than asking you to install them all, we provide two different ways to run everything in your browser: GitHub Codespaces and Binder.
We recommend that you use GitHub Codespaces (see instructions below). If this is not an option for you, you can use Binder, though be aware that the resources might be very limited.

## GitHub codespaces

<p align="center">
  <iframe width="427" height="251" src="https://www.youtube.com/embed/gcAuyqW4QRc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>

In order to use GitHub codespaces, you need to create an account with [GitHub](github.com/) (it's free! You can also enter their education program for additional free stuff).

Click the following button (a new page will open), then return to these instructions:

<p align="center">
  <a href="https://codespaces.new/hsf-training/hsf-training-scikit-hep-webpage?quickstart=1" target="_blank">
    <img src="https://github.com/codespaces/badge.svg" alt="Launch GitHub Codespaces">
  </a>
</p>

After starting the github codespace, take a look at the lines in the "Terminal" tab below. It might take a while until the environment is fully set up. If you see lines like this:

```
Use Cmd/Ctrl + Shift + P -> View Creation Log to see full logs
✔ Finishing up...
✔ Running updateContentCommand...
⠦ Running postCreateCommand...
  › python3 -m pip install -r requirements.txt
```

the installation is still progressing.

Once you're done, you should only see an empty prompt like this:

```
@klieret ➜ /workspaces/hsf-training-scikit-hep-webpage (main) $
```

Wait for a few seconds to see that nothing else runs.

Now look at your left side-panel. You should see a file named `notebook.ipynb`. Click on it.
A new tab opens.
Try to execute the first line to see that everything is set up properly.

You're ready to go 🎉

## Binder

Simply click the following button:

<p align="center">
  <a href="https://mybinder.org/v2/gh/hsf-training/hsf-training-scikit-hep-webpage/main?urlpath=lab" target="_blank">
    <img src="https://mybinder.org/badge_logo.svg" alt="Launch Binder">
  </a>
</p>


# After the tutorial

If you want to install some of these packages on your own or your lab's computer, I recommend [Miniforge](https://github.com/conda-forge/miniforge) (or Anaconda/Miniconda with the [conda-forge channel](https://conda-forge.org/docs/user/introduction.html#how-can-i-install-packages-from-conda-forge)). This method also provides a way to [install ROOT in the same environment](https://github.com/conda-forge/root-feedstock#readme). To setup the environment use the environment.yml file in base of this repository as:


```bash
conda env create --file environment.yml
```

Alternatively, you can install all required packages locally with pip: Take a look at environment.yml for a list (this includes all dependencies listed under the pip key and some of the dependencies listed above).


{% include links.md %}
