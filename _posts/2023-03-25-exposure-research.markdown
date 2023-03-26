---
layout: post
title:  "Working Title"
date:   2023-03-25 01:03:00 -0700
categories: autoexposure shutter speed gain exposure
permalink: ae-research
description: This post delves into the different types of auto exposure 
             algorithms used, the challenges faced in different lighting 
             conditions, and how Auto Exposure technology continues to 
             evolve to produce stunning results. 
image:
  path: ../assets/images/autoexposure/narendra-singh-shekhawat-G3eadnVExhw-unsplash.jpg
  height: 1200
  width: 630
comments: true
---

Auto exposure algorithms are an essential feature of digital cameras, helping to
ensure that images are captured with the appropriate exposure levels in a wide 
range of lighting conditions. I discussed this in great details in my last 
[blog](https://subclassy.github.io/auto-exposure). While these algorithms have 
traditionally been designed with consumer photography in mind, the recent 
academic research has taken a different approach. Instead of focusing on 
consumer goods, the latest research papers are geared towards acquiring the 
best possible images for computer vision tasks and algorithms including 
autonomous vehicles, facial recognition, and virtual reality. To develop more 
accurate and efficient auto exposure algorithms, researchers aim to improve the 
quality of images by using more sophisticated techniques, which is what I will 
be covering in this blog. In this blog, I will delve into a series of five
chronological research papers in auto exposure and examine the innovative 
approaches that are advancing the field of photography and computer vision.

#### Introduction

In the realm of auto exposure research, what I have realized is that the entire
idea of these research papers can be broken down into *four* key areas. 

<ol>
<li><strong>Prediction Model</strong> - The prediction model to determine the
optimal exposure setting for a scene can be based on either deep learning (DL) 
or non-DL approaches. Non-DL based models often use heuristical approaches to 
make predictions. On the other hand, DL-based models use neural networks to 
learn from large datasets of images to predict the optimal combination of gain 
and shutter speed. Some researchers are also developing hybrid approaches that 
combine both DL and non-DL based models to achieve the best of both worlds.</li>

<li><strong>Optimization Metrics</strong> - In addition to prediction models, 
researchers also focus on defining optimization metrics that determine what 
makes a good image or sequence of images. Common optimization metrics used in 
auto exposure research include dynamic range, gradient information, entropy, 
etc. These metrics are used to evaluate the quality of an image captured under
variety of exposure settings.</li>

<li><strong>Algorithm Focus</strong> - The purpose of the algorithms differes 
from paper to paper. Some researchers focus solely on shutter speed, while 
others work on optimizing both shutter speed and gain. As discussed in my last 
<a href="https://subclassy.github.io/auto-exposure">blog</a> shutter speed 
refers to the amount of time that the lens is open and hence it determines the 
amount of light that enters the camera, while gain refers to the amplification 
of the signal to achieve a desired brightness level. There is a third line of 
choice as well where the algorithms are designed to work on an implicit 
definition of "exposure value", rather than breaking it down to explicit 
settings.</li>

<li><strong>Dataset</strong> - Finally, the choice of dataset approach plays a 
critical role in auto exposure research. There are not a lot of public datasets
which conform to the requirements of a specific academic paper. The auther tend
to use specialized datasets that are tailored to specific applications. For 
example, researchers working on autonomous driving might use a dataset of 
images collected from cameras mounted on cars, while those working on facial 
recognition might use a dataset of faces captured under different lighting 
conditions. Hence, this is where data acquisition itself becomes a major 
component of these papers.</li>
</ol>

By considering these four main ideas - prediction model, optimization metric, 
algorithm focus, and dataset approach - researchers are able to develop more 
robust and effective auto exposure algorithms that can be applied across a wide 
range of computer vision applications.

#### Research Papers

In this section, I will talk about the research papers individually. If your
curiousity trumps you, there is a table at the end, where I summarise each of 
these papers according to the above mentioned four criterias.

<subheading><h5>
<a href="https://joonyoung-cv.github.io/assets/paper/14_iros_auto_adjusting.pdf">[1]</a>
<mark>Auto-adjusting Camera Exposure for Outdoor Robotics using Gradient Information</mark>
</h5></subheading>

Shim et al. investigate the use of image gradients as an optimization metric. 
Their premise for this approach is the fact that most computer vision algorithms
work in the gradient domain and hence capturing images with rich gradient 
information is an important first step.

The optimal exposure value that maximises gradient information is determined by 
generating seven synthetic images of the current scene at different exposure 
values and using the computed metric values of the images in a simple
optimization algorithm. The synthetic images are generated using the current 
image and a gamma-correction technique, $$I_{out} = I_{in}^γ$$, where γ = 
[0.1, 0.5, 0.8, 1.0, 1.2, 1.5, 1.9]. So there prediction model is γ 
transformations. The entire process is shown in Figure A.1.

| ![shim_2014](../assets/images/ae-research/shim_2014.png "Figure A.1") | 
|:--:| 
| *Figure A.1 [1]* |

The algorithm proposed by Shim et al., only adjusts the exposure time and 
ignores gain. Which in fact becomes one of the downsides of this approach.
However, in an updated version of their paper, they do mention how in their 
experiments they adjust gain once shutter speed reaches a pre-defined maximum 
value but the whole process of updating gain is left unclear.

Lastly, to validate their algorithm, they used a three camera system, where
the first one runs the built in auto exposure, second one runs the proposed 
algorithm, while the third is set manually by a human. And they use this setup
to test steady state exposures, downstream tasks - like pedestrian detection,
visual odometry, etc.


<subheading><h5>
<a href="https://arxiv.org/pdf/1803.02269.pdf">[2]</a>
<mark>Personalized Exposure Control Using Adaptive Metering and Reinforcement Learning</mark>
</h5></subheading>

The proposed algorithm by Yang et al., is quite unique in the way it tackles the
problem of auto exposure. Their prediction model is a interesting combination
of Convolutional Neural Networks (CNN) and Reinforcement Learning (RL). They 
employ a two-stage approach, where the CNN is used to mimic the behaviour of
a native camera, like the one you find in your smartphones. The goal of this CNN
is to predict the change in exposure value that would be optimal for a given
image. This coarse model is then fine-tuned using RL, where the RL loop 
incoporates a feedback from the user in terms of "under-exposed", "correctly 
exposed", or "over-exposed". This information is then used to fine-tune the 
model to predict the final change in exposure suggested by the model. The entire
process is shown in Figure A.2.  

| ![yang_2018](../assets/images/ae-research/yang_2018.png "Figure A.2") | 
|:--:| 
| *Figure A.2 [2]* |

The way they train their coarse model or the CNN is by supervised learning.
What it means, is that we have a training pair i.e., for every image we provide
as input to the CNN, we know what the corresponding $$\Delta EV$$ is. With these
training pairs, we can train the CNN. The fine-model or RL model, uses policy
gradient with Gaussian policy where the reward is based on user feedback.

This algorithm, uses an implicit definition of "Exposure Value (EV)". The 
purpose of EV is to combine shutter speed and gain into a single parameter. This
is fine for an academic research paper or tools that work with EV. However, 
since the definition of EV is unclear, adapting it to cameras which deal with 
explicit values for shutter speed and gain might be difficult.

In order to train these models, the authors use the Flickr and MIT datasets.
They use Flickr for the coarse model, where they create a synthetic dataset by
passing the images from the dataset through Adobe Lightroom and creating 
training pairs. They use the MIT FiveK dataset for the RL model. Since the images
in this dataset are RAW images, tweaking them to create synthetic images is easier
and more accurate.

<subheading><h6>
<a href="https://arxiv.org/pdf/1907.12646.pdf">[3]</a>
Camera Exposure Control for Robust Robot Vision with Noise-Aware Image Quality Assessment
</h6></subheading>

<subheading><h6>
<a href="https://arxiv.org/pdf/2102.04341.pdf">[4]</a>
Learned Camera Gain and Exposure Control for Improved Visual Feature Detection and Matching
</h6></subheading>


<subheading><h6>
<a href="https://www.mdpi.com/1424-8220/22/3/835/pdf">[4]</a>
Auto-Exposure Algorithm for Enhanced Mobile Robot Localization in Challenging Light Conditions
</h6></subheading>

#### References

[1] M. Hebert, A. Willsky, and Y. Chang, "[Auto-adjusting camera exposure for 
outdoor robotics using gradient information](https://joonyoung-cv.github.io/assets/paper/14_iros_auto_adjusting.pdf)," in Intelligent Robots and Systems,
2014 IEEE/RSJ International Conference on. IEEE, 2014, pp. 2313-2318.

[2] P. W. S. Hung, S. Y. Chen, and C. C. Wu, "[Personalized exposure control 
using adaptive metering and reinforcement learning,](https://arxiv.org/pdf/1803.02269.pdf)" in Proceedings of the 
European Conference on Computer Vision (ECCV), 2018, pp. 606-622.

[3] L. Zhang, Z. Guo, G. Shi, and X. Wang, "[Camera exposure control for robust 
robot vision with noise-aware image quality assessment,](https://arxiv.org/pdf/1907.12646.pdf)" IEEE Robotics and 
Automation Letters, vol. 4, no. 4, pp. 3454-3461, 2019.

[4] H. Liu, R. Wang, H. Kazemzadeh, and N. Barnes, "[Learned camera gain and 
exposure control for improved visual feature detection and matching,](https://arxiv.org/pdf/2102.04341.pdf)" in 
Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern 
Recognition, 2021, pp. 13806-13815.

[5] S. Kim, S. Lee, D. Kim, and S. K. Kim, "[Auto-exposure algorithm for 
enhanced mobile robot localization in challenging light conditions,](https://www.mdpi.com/1424-8220/22/3/835/pdf)" IEEE 
Robotics and Automation Letters, vol. 7, no. 2, pp. 1742-1749, 2022.