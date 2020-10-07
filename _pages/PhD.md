---
layout: archive
title: "PhD Highlights"
permalink: /phd/
author_profile: true

---

{% include base_path %}

Quantitative characterisation
------

A scanning electron microscope collects **multimodal information** from many in-situ detectors.
 
In my PhD, I have been particularly concerned with combining **crystallographic**, structural measurements (from electron diffraction patterns) with **chemical fingerprints** (from stimulated X-ray spectra).
​
In a combinatorial analysis, the **latent feature space represents physical phenomena of engineering interest**, such as nano-scale dispersed particles, chemical inhomogeneity, or local crystallographic ordering.

<p align="center">
<img src="/files/ebsp.png"  width="350">
</p>

<div align="center"> *An electron backscatter diffraction pattern -  unique to a given crystal structure and orientation*


Unsupervised learning
------

We combine the electron backscatter patterns (EBSPs) and energy-dispersive X-ray spectroscopy (EDS) signals into a data matrix for unsupervised learning.
​
The columns of the data matrix are then weighted according to whether we desire a structural (high EBSP variance) or chemical (EDS) driven analysis. [Read more...](https://arxiv.org/abs/1908.04084).

**Principal component analysis** (PCA), **non-negative matrix factorisation** (NMF), and **autoencoder neural networks** can then be employed to investigate the latent feature space, and identify physically significant microstructural properties.


Microstructural analytics
------

Coupled structural and chemical analyses permit rapid, robust, and **intelligible fingerprints** of thermo-mechanically important microstructural constituents. Here we show crystal structure (phase), inverse pole figure (in the Z-out of plane direction), and local chemistry. [Read more...](https://arxiv.org/abs/2009.00948).

Furthermore, our unsupervised learning is sensitive enough to **separate extremely fine-grained detail** due to different levels of lattice ordering (highlighted regions below). This is incredibly important for high temperature strength in gas turbine engine materials. [Read more...](https://arxiv.org/abs/2005.10581).


And a few more highlights...
------

I've developed A MATLAB package for **spherical-angular dark field imaging**, in which images are re-projected onto a diffraction sphere and crystallographically relevant features can be correctly integrated.

I've implemented a **normalisation procedure** for cross-correlation peak heights, in order to determine their **likelihood** based on a fitted lognormal distribution. This permits discarding of poor quality matches, as if the likelihood is too low there is a reasonable probability that the cross-correlation peak height may as well have been sampled randomly. Different structures (space groups) are presented in different colours in (a), and projected onto a standard normal in (b):

For inference of accurate EBSP and sample geometry, I've developed a **gradient descent refinement procedure** based on the cross-correlation peak height to a simulated library. This permits knowledge of important re-projection parameters to a high degree of accuracy.

The below figure plots cross-correlation peak height through the refined template matching (RTM) procedure against refinement iteration step for 5˚ misorientation (a), and average of 100 tests for five initial misorientaitons (5˚ up to 15˚), (b):


