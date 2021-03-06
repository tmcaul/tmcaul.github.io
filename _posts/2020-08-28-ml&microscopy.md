---
title: "ML & microscopy: reinventing the wheel"
date: 2020-08-21
tags:
---

In this piece we consider applying machine learning to a pretty niche field: electron backscatter diffraction. Does it add value? Is it hype? What questions are we better able to ask and answer using modern techniques? Here I don’t delve too much into the maths or physics, or rely too heavily on microscopy concepts — this is meant as a viewpoint on applying ML to a fairly mature domain. This post is also available [here](https://medium.com/@tmcauliffe/ml-microscopy-reinventing-the-wheel-a6702f406f92).

We are probably in a bubble. AI is seeing pretty excessive use in basically every scientific field. Some of it is hype. Some of it is new, genuine insight that wouldn’t have been possible without modern machine learning techniques.

I’m part of a generation of engineers and scientists who first dug in to the ML world as domain experts seeking a new tool in our analytical arsenals. I’ve come to realise that if the setting is right, these can be some of the most powerful inference techniques out there. But, there’s a huge danger in semi-mature fields of reinventing the wheel, and just finding more complicated (and often computationally more expensive) ways of doing what we can already do, for the sake of *hype*.

In this post I’ll have a quick look at a specific branch of electron microscopy, and ways in which ML could and does see genuinely interesting and practical use (disclaimer: I’m biased — I have done a PhD to show it can be helpful).

There are MANY kinds of electron microscope, all specialised to generate high spatial resolution contrast from interesting physical phenomena. We will consider a simple metallic sample (usually a highly symmetric crystal), and a technique called electron backscatter diffraction (EBSD).

<p align="center">
<img src="https://cdn-images-1.medium.com/max/2000/1*SJlyfxnENtCl51Uvwzjo_g.png"  width="420">
</p>

*Figure 1: An incident electron beam on a tilted sample generates a series of crossing bands (an electron backscatter pattern, EBSP).*

In EBSD, an incident electron beam is reflected as a ‘diffraction pattern’ of criss-crossing bands, which are imaged by a CCD. This is an electron backscatter pattern (EBSP). With hundreds of thousands of points in a typical scan, collecting an image consisting of (tens of) thousands of pixels, we end up with some pretty huge datasets. A common setup is schematically shown in **Figure 1**.

An EBSP (specifically the angles, widths, and numbers of the bands) is a strong function of the scanned crystal’s orientation and structure. This often doesn’t change much across an area of interest. A typical experimental EBSP is presented in **Figure 2**.

The process of arriving at an orientation and crystal structure from a measured EBSP is known as indexing. Briefly, the state-of-the art in classical EBSD post-processing involves a comparison (a **cross-correlation**) to templates of simulated patterns, or using a **Hough transform*** to identify band locations, followed by looking up known structures’ inter-band angles. I won’t go into details of these here, but suffice to say: the Hough approach is fast but inaccurate; the cross-correlation / template matching approach can be slow, but is very accurate.

<p align ="center">
<img src="https://cdn-images-1.medium.com/max/2000/1*3R6ce3Ro3nztmqnjR-PSiQ.png"  width="420">
</p>

*Figure 2: An electron backscatter pattern (EBSP) in false colour. The bands correspond to crystal lattice planes, so their intersections and presence reflect the crystal’s symmetry and orientation.*

A typical example of an EBSD scan’s output, known as an **inverse pole figure map**, is presented in **Figure 3**. It describes crystallographic orientation of the material’s *microstructure*. This is of significant engineering use and interest!

![Figure 3: The typical output of an EBSD analysis — a microstructural map showing different features’ crystallographic orientations.](https://cdn-images-1.medium.com/max/2000/1*VDdxngquTwGSOy4y4yi22Q.png)

*Figure 3: The typical output of an EBSD analysis — a microstructural map showing different features’ crystallographic orientations.*

We typically employ AI in the context of either **supervised** or **unsupervised** learning.

In the **supervised** setting, we have an algorithm trained *on some previously labelled data to predict crystal structure and/or orientation (for example a set of Euler angles) from a measured EBSP. This can be very useful, and the work of Shen *et al* **[1]** demonstrates the approach’s power. A convolutional neural network (CNN) is trained to perform an orientation prediction, using *transfer learning *to adapt a simulation-trained model to an experimental setting. A well trained network resulted in EBSP analysis and **orientation determination in < 1 ms**, a substantial improvement on the state-of-the-art (typically 5–15 ms for Hough indexing).

Foden *et al* **[2]** take a slightly different approach, focussing on crystallographic structure classification. In some ways this is a more challenging problem, as the answer to the posed question will depend on *combinations* of features present within an EBSP. By adapting the classic *AlexNet* CNN trained on ImageNet [3], this model was able to identify the features of interest (the criss-crossing bands) that a human, or the Hough transform classical approach, would use to understand this EBSP. The layer-wise activations are published in their paper [2] (and the Masters’ thesis of Alessandro Previero), and are reproduced with permission here in **Figure 4**.

![Figure 4: The activations of a fine-tuned AlexNet CNN trained by Foden *et al* and the Masters’ project of Alessandro Previero, on simulated EBSPs [2]. It picks up on the physically meaningful features: the bands and their intersections.](https://cdn-images-1.medium.com/max/2000/1*kiuHDwUWQC8FFTdTISIUiQ.png)

*Figure 4: The activations of a fine-tuned AlexNet CNN trained by Foden et al and the Masters’ project of Alessandro Previero, on simulated EBSPs [2]. It picks up on the physically meaningful features: the bands and their intersections.*

So it can work. But what’s the benefit over traditional methods? Ultimately, as discussed, **orientation and structure determination is a solved problem**. We can use fast but inaccurate Hough indexing, or slow but accurate template matching. In the supervised setting, it seems that the reason to investigate neural network based techniques, aside from hype, flare, being part of the zeitgeist, *etc*, is a push towards *speed* of solution, so larger areas of interest can be scanned within a reasonable timeframe. This is of course an admirable objective, and it may well be the case that a well-optimised neural network can provide great accuracy at lightning speed. However, we must acknowledge that we are heading towards building a completely opaque *black box*, which sacrifices a rigorous physical justification and model explainability for moderate gains in speed.

Recent work by Foden *et al* **[4]** has shown that there is still a lot of headroom for computational speedup and accuracy in the template matching approach, which could possibly be driven by efficient Fourier transforms and matrix multiplications on the GPU. The crux of the issue is **do we really need to make explainability sacrifices**? This is an open research question, and groups continue to work on all sides of this problem.

Simply predicting the structure or orientation from an input EBSP isn’t the limit of what ML can tell us about our massive dataset. The field of **unsupervised learning** aims to identify latent features within our dataset, correlations between them, and guide us towards things that we consider (by some metric) to be *important*.

This is a problem arguably perfectly suited for a technique like EBSD. We can consider the latent features to be the physically distinct regions of crystal within our scan. These have different structures, orientations, and chemistries. There may be only as few as 1 or 2, or as many as 80–100, unique features in a given scan at a certain magnificaiton. Unsupervised learning at its core helps us reduce our dataset down from the tens of thousands of scan points we have measured down to this **handful of useful features**.

Identifying these features can be achieved using techniques like **principal component analysis** (PCA), as illustrated in **Figure 5**, or **k-means clustering**, as compared by Wilkinson *et al* **[5]**. Many resources for understanding these algorithms are available elsewhere, so I won’t go into too much detail here. These do exactly what was described above: a much reduced dataset basis can be identified that maximally captures the variance of the raw data. This basis corresponds to the latent features we wished to identify.

<p align="center">
<img src="https://cdn-images-1.medium.com/max/2000/1*modFNkgQkqAEJjQffQ2pXA.png">
</p>

*Figure 5: Principal component analysis finds orthogonal basis vectors that best describe the data, so as to retain the maximum amount of total dataset variance.*

In my PhD, we investigated a few more unsupervised learning approaches (**non-negative matrix factorisation** and **autoencoder neural networks**) for the particularly challenging problem of differentiating EBSPs produced from crystals with different chemical *ordering *(how well behaved atoms are at sticking to their designated lattice sites). These EBSPs looked very, very similar, and a human, Hough transform, or template cross-correlation couldn’t tell them apart. The ML reliably could, to differing degrees of success. The paper is available at [https://arxiv.org/abs/2005.10581](https://arxiv.org/abs/2005.10581).

Having access to the latent feature space, identified by unsupervised ML, permits very rapid determination of unique EBSP groupings present in a dataset. We can then use well informed, classical models to understand these. For example, cross-correlation becomes very cheap, and many more structures can be template matched than without the ML [6].

I am certainly very biased, so take my opinion with a pinch of salt. Using ML to uncover latent features in a way that isn’t possible with classical approaches is perhaps a more valuable investment than simply chasing indexing speed. These can then be analysed with **physically justified** and **explainable** approaches.

Over time, supervised neural network algorithms tend to get bigger and more complicated. This can (and does) improve accuracy, but inherently increases the number of **computational operations** required for an analysis. Maybe GPU technology can keep up and this remains a viable approach. On the other hand, well thought out, **physically meaningful**, explainable models (like template matching) are unlikely to get much more computationally complicated. Improvements to these approaches tend to come from better *understanding* the problem rather than simply by adding more parameters to the model. Improvements in computational capability are likely to benefit these approaches more than ever-deepening neural networks.

The issues discussed in this piece are not solely applicable to EBSD, and I have come across similar concerns in other microscopy domains such as **atom probe tomography** and **electron energy loss spectroscopy**. It’s also of interest to completely different fields, such as financial time series modelling where autoregressive linear models such as ARIMA are being displaced by recurrent neural networks, LSTMs, *etc*. As practitioners, we need to be extremely careful that explainability sacrifices we make (and the degrees of abstraction from the raw data we are willing to tolerate) are well justified. It’ll be very interesting to see where different domains land, and the sacrifices they are willing to make for pure performance. I suspect that the physical sciences will be less willing to fully surrender themselves to the deep, dark, black box.

This is just the view of a lowly grad student pretty close to his viva — feel free to get in touch with me on Twitter **@tmcaulf**, I’d love to hear your thoughts.

Many thanks to electron whisperer **@bmatb** for sound-checking my relatively unfounded opinions (and for being somewhat involved in my PhD…).

[1] Y. F. Shen, R. Pokharel, T. J. Nizolek, A. Kumar, and T. Lookman, “Convolutional neural network-based method for real-time orientation indexing of measured electron backscatter diffraction patterns,” *Acta Mater.*, vol. 170, pp. 118–131, 2019.

[2] A. Foden, A. Previero, and T. B. Britton, “Advances in electron backscatter diffraction,” in *40th Risoe International Symposium: Metal Microstructures in 2D, 3D and 4D*, Aug. 2019, vol. 22, no. 11, p. 1261.

[3] A. Krizhevsky, “ImageNet Classification with Deep Convolutional Neural,” *Adv. Neural Inf. Process. Syst.*, 2012.

[4] A. Foden, D. M. Collins, A. J. Wilkinson, and T. B. Britton, “Indexing electron backscatter diffraction patterns with a refined template matching approach,” *Ultramicroscopy*, vol. 207, p. 112845, Dec. 2019.

[5] A. J. Wilkinson, D. M. Collins, Y. Zayachuk, R. Korla, and A. Vilalta-Clemente, “Applications of Multivariate Statistical Methods and Simulation Libraries to Analysis of Electron Backscatter Diffraction and Transmission Kikuchi Diffraction Datasets,” *Ultramicroscopy*, vol. 196, no. June 2018, pp. 88–98, 2018.

[6] T. P. McAuliffe, A. Foden, C. Bilsland, D. Daskalaki-Mountanou, D. Dye, and T. B. Britton, “Advancing characterisation with statistics from correlative electron diffraction and X-ray spectroscopy, in the scanning electron microscope,” *Ultramicroscopy*, no. c, p. 112944, Jan. 2020.
