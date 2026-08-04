# Setup

## For the tutorial

This tutorial uses a sampler of [Scikit-HEP project](https://scikit-hep.org/) packages (Uproot, Awkward Array, hist, Vector, zfit, iminuit, Particle, fastjet), which are all components that you might or might not use in your analysis, as well as Python 3, NumPy, and a smattering of other mainstream libraries (Pandas, Matplotlib, JupyterLab).

Rather than asking you to install them all, we provide two different ways to run everything in your browser: GitHub Codespaces and Binder.
We recommend that you use GitHub Codespaces (see instructions below). If this is not an option for you, you can use Binder, though be aware that the resources might be very limited.

### GitHub codespaces

<p align="center">
  <iframe width="427" height="251" src="https://www.youtube.com/embed/gcAuyqW4QRc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>

In order to use GitHub codespaces, you need to create an account with [GitHub](https://github.com) (it's free! You can also enter their education program for additional free stuff).

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
⠦ Running updateContentCommand...
  › python3 -m pip install -r requirements.txt
```

the installation is still progressing. This will take around 5 minutes, so just be patient.

Once you're done, you should only see an empty prompt like this:

```
@klieret ➜ /workspaces/hsf-training-scikit-hep-webpage (main) $
```

Wait for a few seconds to see that nothing else runs.

You're ready to go 🎉

You can view and run the notebooks from the VSCode interface. However, if you prefer to use JupyterLab, you can append `?editor=jupyter` to the URL in your browser so that it looks like this: `https://<your-codespace-id>.github.dev/?editor=jupyter`. Alternatively, you can go to [your codespaces](https://github.com/codespaces/), find the codespace you just created, click on the three dots on the right side, and select "Open in JupyterLab".

### Binder

Simply click the following button:

<p align="center">
  <a href="https://mybinder.org/v2/gh/hsf-training/hsf-training-scikit-hep-webpage/main?urlpath=lab" target="_blank">
    <img src="https://mybinder.org/badge_logo.svg" alt="Launch Binder">
  </a>
</p>


## After the tutorial

If you want to install some of these packages on your own or your lab's computer, we recommend [Miniforge](https://github.com/conda-forge/miniforge) (or Anaconda/Miniconda with the [conda-forge channel](https://conda-forge.org/docs/user/introduction.html#how-can-i-install-packages-from-conda-forge)). This method also provides a way to [install ROOT in the same environment](https://github.com/conda-forge/root-feedstock#readme). To setup the environment use the [environment.yml](https://github.com/hsf-training/hsf-training-scikit-hep-webpage/blob/main/environment.yml) file in the [root](https://github.com/hsf-training/hsf-training-scikit-hep-webpage) of this repository as:

```bash
conda env create -f environment.yml
```

or you can install individual packages as needed, e.g.,

```bash
conda install uproot awkward   # ... others?
```

Alternatively, you can install all required packages locally with pip with

```bash
pip install -r requirements.txt
```

or individually, e.g.,

```bash
pip install uproot awkward   # ... others?
```

### A note on Windows

Every package used in this tutorial is available for Windows except `xrootd`, which is what lets Uproot read files over `root://` URLs. You can still follow the whole tutorial: the lessons that open a remote file over `root://` also give an `https://` URL for the same file, so uncomment that line instead.

`pip install -r requirements.txt` skips `xrootd` automatically on Windows. The conda `environment.yml` above does not, because conda environment files cannot express per-platform dependencies, so on Windows either use pip, or install into [WSL](https://learn.microsoft.com/en-us/windows/wsl/install), or use GitHub Codespaces or Binder as described above.
