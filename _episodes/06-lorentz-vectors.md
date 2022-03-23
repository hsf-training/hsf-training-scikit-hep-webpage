---
title: "Lorentz vectors, particle PDG, jet-clustering, oh my!"
teaching: 15
exercises: 0
questions:
 - "How do I compute deltaR, mass, coordinate transformations, etc.?"
 - "How do I get masses, decay widths, and other particle properties?"
 - "What about jet-clustering and other specialized algorithms?"
objectives:
 - "Learn about the existence of some helpful Scikit-HEP packages."
keypoints:
 - "Instead of building vector methods into multiple packages, a standalone package provides just that."
 - "The value of these small packages amplify when used together."
---

# Lorentz vectors

In keeping with the "many small packages" philosophy, 2D/3D/Lorentz vectors are handled by a package named Vector. This is where you can find calculations like `deltaR` and coordinate transformations.

~~~
import vector

one = vector.obj(px=1, py=0, pz=0)
two = vector.obj(px=0, py=1, pz=1)

one + two

one.deltaR(two)

one.to_rhophieta()
two.to_rhophieta()
~~~
{: .language-python}

To fit in with the rest of the ecosystem, Vector must be an array-oriented library. Arrays of 2D/3D/Lorentz vectors are processed in bulk.

`MomentumNumpy2D`, `MomentumNumpy3D`, `MomentumNumpy4D` are NumPy array subtypes: NumPy arrays can be *cast* to these types and get all the vector functions.

~~~
import skhep_testdata, uproot
import awkward as ak

tree = uproot.open(skhep_testdata.data_path("uproot-Zmumu.root"))["events"]

one = ak.to_numpy(tree.arrays(filter_name=["E1", "p[xyz]1"]))
two = ak.to_numpy(tree.arrays(filter_name=["E2", "p[xyz]2"]))

one.dtype.names = ("E", "px", "py", "pz")
two.dtype.names = ("E", "px", "py", "pz")

one = one.view(vector.MomentumNumpy4D)
two = two.view(vector.MomentumNumpy4D)

one + two

one.deltaR(two)

one.to_rhophieta()
two.to_rhophieta()
~~~
{: .language-python}

After `vector.register_awkward()` is called, `"Momentum2D"`, `"Momentum3D"`, `"Momentum4D"` are record names that Awkward Array will recognize to get all the vector functions.

~~~
vector.register_awkward()

tree = uproot.open(skhep_testdata.data_path("uproot-HZZ.root"))["events"]

array = tree.arrays(filter_name=["Muon_E", "Muon_P[xyz]"])

muons = ak.zip({"px": array.Muon_Px, "py": array.Muon_Py, "pz": array.Muon_Pz, "E": array.Muon_E}, with_name="Momentum4D")
mu1, mu2 = ak.unzip(ak.combinations(muons, 2))

mu1 + mu2

mu1.deltaR(mu2)

muons.to_rhophieta()
~~~
{: .language-python}

# Particle PDG

The Particle library provides all of the particle masses, decay widths, etc.

~~~
import particle
from hepunits import GeV

particle.Particle.from_string("p~")

z_boson = particle.Particle.from_string("Z0")
z_boson.mass / GeV, z_boson.width / GeV

particle.Particle.from_pdgid(111)

particle.Particle.findall(lambda p: p.pdgid.is_meson and p.pdgid.has_strange and p.pdgid.has_charm)
~~~
{: .language-python}

# Jet clustering

Some people need to do jet-clustering at the analysis level. The fastjet package makes it possible to do that an (Awkward) array at a time.

~~~
picodst = uproot.open("https://pivarski-princeton.s3.amazonaws.com/pythia_ppZee_run17emb.picoDst.root:PicoDst")
px, py, pz = ak.unzip(picodst.arrays(filter_name=["Track/Track.mPMomentum[XYZ]"], entry_stop=100))

probable_mass = particle.Particle.from_string("pi+").mass / GeV

pseudojets = ak.zip({"px": px, "py": py, "pz": pz, "mass": probable_mass}, with_name="Momentum4D")
good_pseudojets = pseudojets[pseudojets.pt > 0.1]

import fastjet

jetdef = fastjet.JetDefinition(fastjet.antikt_algorithm, 1.0)

clusterseq = fastjet.ClusterSequence(good_pseudojets, jetdef)
clusterseq.inclusive_jets()

ak.num(good_pseudojets), ak.num(clusterseq.inclusive_jets())
~~~
{: .language-python}

This fastjet package uses Vector to get coordinate transformations and all the Lorentz vector methods you're accustomed to having in pseudo-jet objects. I used Particle to impute the mass of particles with only track-level information.

See how all the pieces accumulate?

{% include links.md %}
