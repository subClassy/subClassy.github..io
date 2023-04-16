---
layout: post
title:  "Bio-inspired Computer Vision"
date:   2023-04-08 14:53:00 -0700
categories: compression generative ai video deep-learning
permalink: deep-compression
description: Recent research papers demonstrate the potential of generative models
             to significantly improve the quality of compressed video while 
             reducing the amount of data required to store and transmit it. In 
             this blog, I summarize four of these papers and explore the ways 
             in which generative models are revolutionizing video compression. 
image:
  path: ../assets/images/deep-video-compression/chuttersnap-E2mCFyp5D5E-unsplash.jpg
  height: 1200
  width: 630
comments: true
---

The human eye is a remarkable organ that can capture light and convert it into 
electrical signals that are processed by the brain. The idea of using a camera 
to capture images dates back to ancient times, but the earliest camera-like 
devices were actually inspired by the human eye. The first known camera obscura 
was described by the ancient Chinese philosopher Mozi in the 5th century BCE [1].
Hence, it is no surprise that biology has long served as a source of 
inspiration for engineering, with engineers drawing on the natural world to 
develop new technologies and improve existing ones. Many synonymous terms like 
bionics, biomimetics, or bioinspired engineering have been used for the flow of 
concepts from biology to engineering.

This idea of using biology for vision based applications led to the generation 
of Bio-inspired computer vision as a field of study that takes inspiration from 
biological vision systems, such as the human eye and brain, to develop computer 
vision systems that can see and process visual information in a way similar to 
humans. But this source of inspiration is not just limited to the human biology.
In fact, one of the first instances of bio-inspired vision algorithms began with
two young soldiers during World War II. A biology student, Bernhard Hassenstein, 
met a physicist, Werner Reichardt. They did a series of elegant experiments, 
using the optomotor response of the *beetle Cholorphanus* [Fig A.1] as a behavioral 
measure. This response is the animal's tendency to follow the movement of the 
visual surround to compensate for its mistaken perception of self-motion in the
opposite direction. 

| ![beetle](../assets/images/bio-vision/beetle.webp "Figure A.1") | ![Hassenstein-Reichardt Detector](../assets/images/bio-vision/hrd.webp "Figure A.2") |
|:--:| :--:| 
| *Figure A.1* | *Figure A.2* |

Their results led to the development of a model for *motion 
detection* that became known as the <strong>'Hassenstein-Reichardt Detector'[Fig A.2]</strong>. The core 
computation in this model is a delay-and-compare mechanism: delaying the 
brightness signal as measured by one photoreceptor by a low-pass filter and 
comparing it by multiplication with the instantaneous signal derived from a 
neighboring location. Doing this twice in a mirror-symmetrical fashion and 
subtracting the output signals of both subunits leads to a response that is 
fully directionally selective[2]. 

The Hassenstein-Reichardt detector, a model of motion detection in insects, has 
been an important inspiration for the development of bio-inspired technologies 
today. Neuromorphic cameras are one such example of bio-inspired computer 
vision. These cameras are designed to mimic the way the human retina processes 
visual information and send it to the brain. The term neuromorphic was coined
by Carver Mead in his groundbreaking paper in 1990 [6].

<figure class="neuromorphic" align="right">
    <img src="../assets/images/bio-vision/neuromorphic.jpg" alt="neuromorphic_cameras" width="300"/>
    <figcaption>Figure A.3 [3]</figcaption>
</figure>

A neuromorphic camera consists of an array of sensors that mimic the structure 
of the retina [Fig A.3]. Each sensor detects changes in light intensity and sends a 
signal to a processing unit that mimics the behavior of neurons in the brain. 
These processing units work together to analyze the visual information and 
extract features, such as edges and shapes, from the scene. The resulting 
output is a spatio-temporal stream of data instead of conventional frames
that represents the visual information. A really interesting read on this topic 
is the chapter on Neuromorphic Vision in the book <strong>Biologically Inspired 
Computer Vision: Fundamentals and Applications</strong> [7].

One of the main advantages of neuromorphic cameras is their low power 
consumption. Unlike traditional cameras that require a lot of power to capture 
and process images, neuromorphic cameras are designed to operate efficiently and
consume much less power. This makes them ideal for applications where power is 
limited, such as in portable devices and autonomous systems. In fact, NASA has 
deigned <strong>Falcon Neuro</strong>[4] which is a payload to be flown on the International 
Space Station (ISS)[Fig A.4] to detect lightning and sprites from low earth orbit using 
Neuromorphic cameras. Falcon Neuro consists of two neuromorphic cameras, 
one nadir, and one directed ram. Both cameras work in the visible spectrum, and 
have a 10 degree field of view. The nadir camera will be used to detect 
lightning, while the ram camera is designed to detect sprites. 

| ![falcon_neuro](../assets/images/bio-vision/Project%20Falcon%20Neuro%20NASA.jpg "Figure A.4") | 
|:--:| 
| *Figure A.4 [5]* |

Another natural application of neuromorphic vision sensors is in electronic 
retinal implants for restoring sight to those whose vision has been lost to 
disease. Pixium Vision, a French company, has developed a neuromorphic 
retinal implant, which use event-based sampling to provide patients with visual 
stimulation [8]. 

This whole idea of using data spikes to represent visual information has also 
been integrated into the design of deep neural networks[9]. This idea is to 
apply the lessons learnt from several decades of research in deep learning, 
gradient descent, backpropagation and neuroscience to biologically plausible 
spiking neural neural networks.

| ![snns](../assets/images/bio-vision/spike_excite_alpha_ps2.gif "Figure A.5") | 
|:--:| 
| *Figure A.5* |


Artifical Neural Networks(ANNs) and Spiking NNs (SNNs) can model the same types 
of network topologies, but SNNs trade the artificial neuron model with a 
spiking neuron model instead [Figure A.5]. Much like the artificial neuron 
model, spiking neurons operate on a weighted sum of inputs. Rather than passing
the result through a nonlinearity, the weighted sum contributes to the 
membrane potential of the neuron. If the neuron is sufficiently excited by this 
weighted sum, and the membrane potential reaches a threshold, then the neuron 
will emit a spike to its subsequent connections. But since neuronal inputs are 
spikes of very short bursts of electrical activity there is an integrated 
temporal dynamics that ‘sustain’ the membrane potential over time. The authors
of the paper have any packaged this whole architecture into a python library
['snnTorch'](https://snntorch.readthedocs.io/en/latest/index.html).

In summary, the field of bio-inspired computer vision is a very exciting area. 
Bio-inspired vision technology is even starting to outperform conventional, 
frame-based vision systems in many application fields and to establish new 
benchmarks in terms of redundancy suppression and data compression, DR, temporal
resolution, and power efficiency. Demanding vision tasks such as real-time 3D 
mapping, complex multiobject tracking, or fast visual feedback loops for 
sensory–motor action, tasks that often pose severe, sometimes insurmountable, 
challenges to conventional artificial vision systems, are in reach using 
bioinspired vision sensing and processing techniques.


#### References

[1] [http://www.photographyhistoryfacts.com/photography-development-history/camera-obscura-history/](http://www.photographyhistoryfacts.com/photography-development-history/camera-obscura-history/)

[2] Borst, A. Models of motion detection. Nat Neurosci 3 (Suppl 11), 1168 (2000). https://doi.org/10.1038/81435

[3] [https://www.westernsydney.edu.au/future-makers/issue-five/biology-inspired-cameras-on-the-international-space-station](https://www.westernsydney.edu.au/future-makers/issue-five/biology-inspired-cameras-on-the-international-space-station)

[4] McHarg, M. G., “Falcon Neuro—Neuromorphic Cameras for sprite and lightning detection on the International Space Station”, vol. 2020, 2020.

[5] [https://spaceaustralia.com/index.php/news/world-first-neuromorphic-data-received-space-station](https://spaceaustralia.com/index.php/news/world-first-neuromorphic-data-received-space-station)

[6] Mead, C. (1990) Neuromorphic electronic systems. Proc. IEEE, 78(10), 1629– 1636.

[7] Herault, Jeanny. Biologically inspired computer vision: fundamentals and applications. John Wiley & Sons, 2015.ch2

[8] Posch, Christoph, Ryad Benosman, and Ralph Etienne-Cummings. "Giving machines humanlike eyes." IEEE Spectrum 52.12 (2015): 44-49.

[9] Eshraghian, Jason K., et al. "Training spiking neural networks using lessons from deep learning." arXiv preprint arXiv:2109.12894 (2021).