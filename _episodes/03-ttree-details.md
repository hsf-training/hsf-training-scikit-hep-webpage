---
title: "TTree details"
teaching: 25
exercises: 0
questions:
 - "What are TBranches and TBaskets?"
 - "How can I read multiple TBranches at once?"
 - "How can I get ROOT data into NumPy or Pandas?"
objectives:
 - "Learn how to read over TTrees efficiently, including common pitfalls."
 - "Learn how to turn interactive tinkering into a scaled-up job."
 - "Learn how to send data directly into NumPy or Pandas."
keypoints:
 - "ROOT files have a structure that enables partial reading. This is essential for large datasets."
 - "Be aware of how much data you're reading and when."
 - "The Python + Jupyter + Uproot interface provides a gradual path from interactive tinkering to scaled-up workflows."
---

# ROOT file structure and terminology

A ROOT file ([ROOT TFile](https://root.cern.ch/doc/master/classTFile.html), [uproot.ReadOnlyFile](https://uproot.readthedocs.io/en/latest/uproot.reading.ReadOnlyFile.html)) is like a little filesystem containing nested directories ([ROOT TDirectory](https://root.cern.ch/doc/master/classTDirectory.html), [uproot.ReadOnlyDirectory](https://uproot.readthedocs.io/en/latest/uproot.reading.ReadOnlyDirectory.html)). In Uproot, nested directories are presented as nested dicts.

Any class instance ([ROOT TObject](https://root.cern.ch/doc/master/classTObject.html), [uproot.Model](https://uproot.readthedocs.io/en/latest/uproot.model.Model.html)) can be stored in a directory, including types such as histograms (e.g. [ROOT TH1](https://root.cern.ch/doc/master/classTH1.html), [uproot.behaviors.TH1.TH1](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TH1.TH1.html)).

One of these classes, TTree ([ROOT TTree](https://root.cern.ch/doc/master/classTTree.html), [uproot.TTree](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html)), is a gateway to large datasets. A TTree is roughly like a Pandas DataFrame in that it represents a table of data. The columns are called TBranches ([ROOT TBranch](https://root.cern.ch/doc/master/classTBranch.html), [uproot.TBranch](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TBranch.TBranch.html)), which can be nested (unlike Pandas), and the data can have any C++ type (unlike Pandas, which can store any Python type).

A TTree is often too large to fit in memory, and sometimes (rarely) even a single TBranch is too large to fit in memory. Each TBranch is therefore broken down into TBaskets ([ROOT TBasket](https://root.cern/doc/master/classTBasket.html), [uproot.models.TBasket.Model_TBasket](https://uproot.readthedocs.io/en/latest/uproot.models.TBasket.Model_TBasket.html)), which are "batches" of data. (These are the same batches that each call to `extend` writes in the previous lesson.) TBaskets are the smallest unit that can be read from a TTree: if you want to read the first entry, you have to read the first TBasket.

![terminology]({{ page.root }}/fig/terminology.png)

As a data analyst, you'll likely be concerned with TTrees and TBranches first-hand, but only TBaskets when efficiency issues come up. Files with large TBaskets might require a lot of memory to read; files with small TBaskets will be slower to read (in ROOT also, but especially in Uproot). Megabyte-sized TBaskets are usually ideal.

# Examples with a large TTree

[This file](http://opendata.web.cern.ch/record/12341) is 2.1 GB, hosted by CERN's Open Data Portal.

~~~
import uproot
file = uproot.open("root://eospublic.cern.ch//eos/opendata/cms/derived-data/AOD2NanoAODOutreachTool/Run2012BC_DoubleMuParked_Muons.root")
file.classnames()
~~~
{: .language-python}

> ## Why the `;74` and `;75`?
>
> You may have been wondering about the numbers after the semicolons. These are ROOT "cycle numbers," which allow objects with the same name to be distinguishable. They're used when an object needs to be overwritten as it grows without losing the last valid copy of that object, so that a ROOT file can be read even if the writing process failed partway through.
>
> In this case, the last version of this TTree was number 75, and number 74 is the second-to-last.
>
> If you don't specify cycle numbers, Uproot will pick the last for you, which is almost always what you want. (In other words, you can ignore them.)
{: .callout}

Just asking for the [uproot.TTree](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html) object and printing it out *does not* read the whole dataset.

~~~
tree = file["Events"]
tree.show()
~~~
{: .language-python}

## Reading part of a TTree

In the last lesson, we learned that the most direct way to read one TBranch is to call [uproot.TBranch.array](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TBranch.TBranch.html#array).

~~~
tree["nMuon"].array()
~~~
{: .language-python}

However, it takes a long time because a lot of data have to be sent over the network.

To limit the amount of data read, set `entry_start` and `entry_stop` to the range you want. The `entry_start` is inclusive, `entry_stop` exclusive, and the first entry would be indexed by `0`, just like slices in an array interface (first lesson). Uproot only reads as many TBaskets as are needed to provide these entries.

~~~
tree["nMuon"].array(entry_start=1000, entry_stop=2000)
~~~
{: .language-python}

These are the building blocks of a parallel data reader: each is responsible for a different slice. (See also [uproot.TTree.num_entries_for](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#num-entries-for) and [uproot.TTree.common_entry_offsets](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#common-entry-offsets), which can be used to pick `entry_start`/`entry_stop` in optimal ways.)

## Reading multiple TBranches at once

Suppose you know that you will need all of the muon TBranches. Asking for them in one request is more efficient than asking for each TBranch individually because the server can be working on reading the later TBaskets from disk while the earlier TBaskets are being sent over the network to you. Whereas a TBranch has an `array` method, the TTree has an `arrays` (plural) method for getting multiple arrays.

~~~
muons = tree.arrays(["Muon_pt", "Muon_eta", "Muon_phi", "Muon_mass", "Muon_charge"], entry_stop=1000)
muons
~~~
{: .language-python}

Now all five of these TBranches are in the output, `muons`, which is an Awkward Array. An Awkward Array of multiple TBranches has a dict-like interface, so we can get each variable from it by

~~~
muons["Muon_pt"]
muons["Muon_eta"]
muons["Muon_phi"]   # etc.
~~~
{: .language-python}

> ## Beware! It's `tree.arrays` that actually reads the data!
>
> If you're not careful with the [uproot.TTree.arrays](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#arrays) call, you could end up waiting a long time for data you don't want or you could run out of memory. Reading everything with
>
> ~~~
> everything = tree.arrays()
> ~~~
> {: .language-python}
>
> and then picking out the arrays you want is usually not a good idea. At the very least, set an `entry_stop`.
{: .callout}

## Selecting TBranches by name

Suppose you have many muon TBranches and you don't want to list them all. The [uproot.TTree.keys](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#keys) and [uproot.TTree.arrays](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#arrays) both take a `filter_name` argument that can select them in various ways (see documentation). In particular, it's good to use the `keys` first, to know which branches match your filter, followed by `arrays`, to actually read them.

~~~
tree.keys(filter_name="Muon_*")

tree.arrays(filter_name="Muon_*", entry_stop=1000)
~~~
{: .language-python}

(There are also `filter_typename` and `filter_branch` for more options.)

## Scaling up, making a plot

The best way to figure out what you're doing is to tinker with small datasets, and then scale them up. Here, we take 1000 events and compute dimuon masses.

~~~
muons = tree.arrays(entry_stop=1000)
cut = (muons["nMuon"] == 2)

pt0  = muons["Muon_pt",  cut, 0]; pt1  = muons["Muon_pt",  cut, 1]
eta0 = muons["Muon_eta", cut, 0]; eta1 = muons["Muon_eta", cut, 1]
phi0 = muons["Muon_phi", cut, 0]; phi1 = muons["Muon_phi", cut, 1]

import numpy as np
mass = np.sqrt(2*pt0*pt1*(np.cosh(eta0 - eta1) - np.cos(phi0 - phi1)))

import hist
masshist = hist.Hist(hist.axis.Regular(120, 0, 120, label="mass [GeV]"))
masshist.fill(mass)
masshist.plot()
~~~
{: .language-python}

That worked (there's a Z peak). Now to do this over the whole file, we should be more careful about what we're reading,

~~~
tree.keys(filter_name=["nMuon", "/Muon_(pt|eta|phi)/"])
~~~
{: .language-python}

and accumulate data gradually with [uproot.TTree.iterate](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html#iterate). This handles the `entry_start`/`entry_stop` in a loop.

~~~
masshist = hist.Hist(hist.axis.Regular(120, 0, 120, label="mass [GeV]"))

for muons in tree.iterate(filter_name=["nMuon", "/Muon_(pt|eta|phi)/"]):
    cut = (muons["nMuon"] == 2)
    pt0  = muons["Muon_pt",  cut, 0]; pt1  = muons["Muon_pt",  cut, 1]
    eta0 = muons["Muon_eta", cut, 0]; eta1 = muons["Muon_eta", cut, 1]
    phi0 = muons["Muon_phi", cut, 0]; phi1 = muons["Muon_phi", cut, 1]

    mass = np.sqrt(2*pt0*pt1*(np.cosh(eta0 - eta1) - np.cos(phi0 - phi1)))
    masshist.fill(mass)

    print(masshist.sum() / tree.num_entries)

masshist.plot()
~~~
{: .language-python}

## Getting data into NumPy or Pandas

In all of the above examples, the `array`, `arrays`, and `iterate` methods return Awkward Arrays. The Awkward Array library is useful for exactly this kind of data (jagged arrays: more in the next lesson), but you might be working with libraries that only recognize NumPy arrays or Pandas DataFrames.

Use `library="np"` or `library="pd"` to get NumPy or Pandas, respectively.

~~~
tree["nMuon"].array(library="np", entry_stop=10000)

tree.arrays(library="np", entry_stop=10000)

tree.arrays(library="pd", entry_stop=10000)
~~~
{: .language-python}

NumPy is great for non-jagged data like the `"nMuon"` branch, but it has to represent an unknown number of muons per event as an array of NumPy arrays (i.e. Python objects).

Pandas can be made to represent multiple particles per event by putting this structure in a [pd.MultiIndex](https://pandas.pydata.org/pandas-docs/stable/user_guide/advanced.html), but not when the DataFrame contains more than one particle type (e.g. muons *and* electrons). Use separate DataFrames for these cases. If it helps, note that there's another route to DataFrames: by reading the data as an Awkward Array and calling [ak.to_pandas](https://awkward-array.readthedocs.io/en/latest/_auto/ak.to_pandas.html) on it. (Some methods use more memory than others, and I've found Pandas to be unusually memory-intensive.)

Or use Awkward Arrays (next lesson).

{% include links.md %}
