---
layout: post
title:  "The Evolution of Auto Exposure Algorithms"
date:   2023-03-25 01:03:00 -0700
categories: auto-exposure shutter speed gain exposure
permalink: ae-research
description: Explore the cutting-edge innovations in auto-exposure algorithms 
             for computer vision tasks. In this blog, I 
             discuss five recent research papers and talk about the 
             advancements in this fascinating field. 
image:
  path: ../assets/images/ae-research/dan-dimmock-3mt71MKGjQ0-unsplash.jpg
  height: 1200
  width: 630
comments: true
---

Auto exposure algorithms are an essential feature of digital cameras, helping to
ensure that images are captured with the appropriate exposure levels in a wide 
range of lighting conditions. I discussed this in great detail in my last 
[blog](https://subclassy.github.io/auto-exposure). While these algorithms have 
traditionally been designed with consumer photography in mind, recent academic 
research has taken a different approach. Instead of focusing on 
consumer goods, the latest research papers are geared toward acquiring the 
best possible images for computer vision tasks and algorithms including 
autonomous vehicles, facial recognition, and virtual reality. To develop more 
accurate and efficient auto exposure algorithms, researchers aim to improve the 
quality of images by using more sophisticated techniques, which is what I will 
be covering in this blog. In this blog, I will delve into a series of five
chronological research papers on auto exposure and examine the innovative 
approaches that are advancing the field of photography and computer vision.

#### Introduction

In the realm of auto-exposure research, what I have realized is that the entire
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
auto-exposure research include dynamic range, gradient information, entropy, 
etc. These metrics are used to evaluate the quality of an image captured under
a variety of exposure settings.</li>

<li><strong>Algorithm Focus</strong> - The purpose of the algorithms differs 
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
critical role in auto-exposure research. There are not a lot of public datasets
which conform to the requirements of a specific academic paper. The author tends
to use specialized datasets that are tailored to specific applications. For 
example, researchers working on autonomous driving might use a dataset of 
images collected from cameras mounted on cars, while those working on facial 
recognition might use a dataset of faces captured under different lighting 
conditions. Hence, this is where data acquisition itself becomes a major 
component of these papers.</li>
</ol>

By considering these four main ideas - prediction model, optimization metric, 
algorithm focus, and dataset approach - researchers are able to develop more 
robust and effective auto-exposure algorithms that can be applied across a wide 
range of computer vision applications.

#### Research Papers

In this section, I will talk about the research papers individually. If your 
curiosity trumps you, there is a <a href="#research-table-id">table</a> at the end, where I summarise each of 
these papers according to the above-mentioned four criteria.

<subheading><h5>
<a href="https://joonyoung-cv.github.io/assets/paper/14_iros_auto_adjusting.pdf">[1]</a>
<mark>Auto-adjusting Camera Exposure for Outdoor Robotics using Gradient Information</mark>
</h5></subheading>

Shim et al. investigate the use of image gradients as an optimization metric. 
Their premise for this approach is the fact that most computer vision algorithms
work in the gradient domain and hence capturing images with rich gradient 
information is an important first step.

The optimal exposure value that maximizes gradient information is determined by 
generating seven synthetic images of the current scene at different exposure 
values and using the computed metric values of the images in a simple
optimization algorithm. The synthetic images are generated using the current 
image and a gamma-correction technique, $$I_{out} = I_{in}^γ$$, where γ = 
[0.1, 0.5, 0.8, 1.0, 1.2, 1.5, 1.9]. So their prediction model is γ 
transformations. The entire process is shown in Figure A.1.

| ![shim_2014](../assets/images/ae-research/shim_2014.png "Figure A.1") | 
|:--:| 
| *Figure A.1 [1]* |

The algorithm proposed by Shim et al. only adjusts the exposure time and 
ignores gain. Which in fact becomes one of the downsides of this approach.
However, in an updated version of their paper, they do mention how in their 
experiments they adjust gain once shutter speed reaches a pre-defined maximum 
value but the whole process of updating gain is left unclear.

Lastly, to validate their algorithm, they used a three-camera system, where
the first one runs the built-in auto exposure, the second one runs the proposed 
algorithm, and the third is set manually by a human. And they use this setup
to test steady-state exposures, and downstream tasks - like pedestrian detection,
visual odometry, etc.


<subheading><h5>
<a href="https://arxiv.org/pdf/1803.02269.pdf">[2]</a>
<mark>Personalized Exposure Control Using Adaptive Metering and Reinforcement Learning</mark>
</h5></subheading>

The proposed algorithm by Yang et al. is quite unique in the way it tackles the
problem of auto exposure. Their prediction model is an interesting combination 
of Convolutional Neural Networks (CNN) and Reinforcement Learning (RL). They 
employ a two-stage approach, where the CNN is used to mimic the behavior of a 
native camera, like the one you find in your smartphones. The goal of this CNN
is to predict the change in exposure value that would be optimal for a given
image. This coarse model is then fine-tuned using RL, where the RL loop 
incorporates feedback from the user in terms of "under-exposed", "correctly 
exposed", or "over-exposed". This information is then used to fine-tune the 
model to predict the final change in exposure suggested by the model. The entire
process is shown in Figure A.2.  

| ![yang_2018](../assets/images/ae-research/yang_2018.png "Figure A.2") | 
|:--:| 
| *Figure A.2 [2]* |

The way they train their coarse model or the CNN is by supervised learning.
What it means, is that we have a training pair i.e., for every image we provide 
as an input to the CNN, we know what the corresponding $$\Delta EV$$ is. With these
training pairs, we can train the CNN. The fine model or RL model uses a 
policy gradient with a Gaussian policy where the reward is based on user feedback.

This algorithm uses an implicit definition of "Exposure Value (EV)". The purpose
of the EV is to combine shutter speed and gain into a single parameter. This
is fine for an academic research paper or tools that work with EV. However, 
since the definition of EV is unclear, adapting it to cameras that deal with 
explicit values for shutter speed and gain might be difficult.

In order to train these models, the authors use the Flickr and MIT datasets.
They use Flickr for the coarse model, where they create a synthetic dataset by
passing the images from the dataset through Adobe Lightroom and creating 
training pairs. They use the MIT FiveK dataset for the RL model. Since the images
in this dataset are RAW images, tweaking them to create synthetic images is easier
and more accurate.

<subheading><h5>
<a href="https://arxiv.org/pdf/1907.12646.pdf">[3]</a>
<mark>Camera Exposure Control for Robust Robot Vision with Noise-Aware Image Quality Assessment</mark>
</h5></subheading>

This algorithm by Shin et al. has an underlying premise similar to what was 
proposed by Shim et al. in the first paper. Instead of just maximizing the 
gradient information, it uses a combination of gradient information, global
image entropy which encapsulates basic image attributes like color, contrast and 
brightness, and a noise-based metric. The first two metrics - gradient and entropy
are supposed to be maximized while noise should be minimized. So they use a weighted
cost function that they optimize for.

Unlike Shim's paper, where they use γ-transformation as a prediction model, 
Shin et al., do not use any prediction model. In order to calculate metric 
information at a variety of exposure levels, it simply triggers the camera to 
capture a new image at the latest exposure setting. To determine exposure settings
at which to capture the images, the algorithm uses Nelder-Mead optimization. 
The Nelder-Mead optimization is a method for finding the minimum or maximum 
value of a function without using any derivative information. It works by 
iteratively modifying a set of points (called a simplex) in the search space until the best point 
is found. The initial point in this case the initial shutter speed and gain values.
I have explained this whole process in a simplified diagram in Figure A.3.

| ![shin_2019](../assets/images/ae-research/shin_2019.png "Figure A.3") | 
|:--:| 
| *Figure A.3* |

The good thing about this algorithm is that this works with explicit definitions
of shutter speed and gain. And the optimization loop covers the search space for both these parameters. 

For their experiments, they also capture their own dataset. They define a search
space for both shutter and gain depending on indoor vs outdoor static scenes. Then
they capture 550 images at all possible combinations of shutter and gain within
the search space.

<subheading><h5>
<a href="https://arxiv.org/pdf/2102.04341.pdf">[4]</a>
<mark>Learned Camera Gain and Exposure Control for Improved Visual Feature Detection and Matching</mark>
</h5></subheading>

This by far has been my favorite paper to read. Tomasi et al. in this paper
propose the use of a deep convolutional neural network model to predictively 
adjust camera gain and exposure time parameters. They propose a CNN that takes 
as input a sequence of the past three frames along with the respective camera 
parameter settings and regresses adjustments to the exposure time and
gain. The sequential input of three images ensures that temporal information is 
passed into the network to observe how the images are changing, while the 
inclusion of the current gain/exposure allows the network to decouple the 
changes due to varying parameters from the changes due to varying external 
illumination.

They train the above CNN in a self-supervised fashion. In order to do this they
use a combination of the number of features found in the most recently captured 
image and the number of inlier feature matches between sequential
images as an optimization metric. To find the training targets for such an 
approach they use a window of four frames to identify the target shutter and 
gain for each given frame. This way, they eliminate any manual labeling of the 
data.

| ![tomasi_2021](../assets/images/ae-research/tomasi_2021.png "Figure A.4") | 
|:--:| 
| *Figure A.4 [4]* |

As shown in Figure A.4, the network takes as input a sequence of images 
$${I_t, I_{t−1}, I_{t−2}}$$ and the corresponding gain $${G_t, G_{t−1}, G_{t−2}}$$
and exposure $${E_t, E_{t−1}, E_{t−2}}$$ values, and outputs the next gain 
$$G_{t+1}$$ and exposure $$E_{t+1}$$ settings that predictively maximize the 
number of inlier feature matches in future image frames.

To train this network, they use a sampling-based data collection procedure using
a dual-camera configuration, with two identical cameras mounted side-by-side.
Further, their sampling strategy involves capturing images while moving, which 
ensures that we account for motion blur due to changes in exposure time. To 
ensure that they effectively sample values that are near the optimal region 
of the parameter space, they use an ‘informed’ sampling approach. Their dataset
collection is quite intriguing and something I recommend going ahead read in
detail.

<subheading><h5>
<a href="https://www.mdpi.com/1424-8220/22/3/835/pdf">[4]</a>
<mark>Auto-Exposure Algorithm for Enhanced Mobile Robot Localization in Challenging Light Conditions</mark>
</h5></subheading>

This paper by Begin et al. is one of the latest papers in this line of research.
This feels like a very direct improvement over the first paper by Shim et al.
In this paper, they have directly addressed each component of the paper by Shim
and proposed an improvement or an additional module over it.

Instead of using gamma-correction, they propose the use of Photometric Response
Function(PRF)-based transformations. A PRF function maps the exposure of a 
camera sensor to the intensity of the image. They also add saturation feedback 
to compensate for the prediction errors due to image saturation. 

The optimization metric they use is the same as Shim's algorithm - Gradient 
Information. 

Unlike Shim's algorithm, which does not deal with exposure and gain explicitly,
Begin et al. propose a comprehensive update strategy for both shutter and gain
that minimizes motion blur and noise. They balance gain and shutter using a single
constant factor that weighs the relative importance of image noise and motion 
blue depending on the target application. 

The experimental setup used in their work and shown comprises two cameras. All 
experiments are performed with the cameras mounted on a custom three-axis 
(xy–yaw) motion table. The first camera runs the built-in AE algorithm, while
the second one runs the proposed algorithm. The cameras then run through a 
pre-defined path following a set of textured targets and determining matched
features across these targets.

#### Summary

The following table consists of a summary of the above five algorithms - 

<a id="research-table-id">
<table class="research-table">
    <thead>
        <tr>
            <th>Paper Name</th>
            <th>Year</th>
            <th>Prediction Model</th>
            <th>Optimization Metric</th>
            <th>Algorithm Focus</th>
            <th>Dataset Approach</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Auto-adjusting Camera Exposure for Outdoor Robotics using Gradient Information</td>
            <td>2014</td>
            <td>Gamma Transformations</td>
            <td>Gradient Information</td>
            <td>Exposure</td>
            <td>Three-camera system to run experiments and comparison</td>
        </tr>
        <tr>
            <td>Personalized Exposure Control Using Adaptive Metering and Reinforcement Learning</td>
            <td>2018</td>
            <td>CNN + RL</td>
            <td>Supervised training and user feedback</td>
            <td>Exposure Value</td>
            <td>Flickr and MIT FiveK datasets</td>
        </tr>
        <tr>
            <td>Camera Exposure Control for Robust Robot Vision with Noise-Aware Image Quality Assessment</td>
            <td>2019</td>
            <td>None [Nelder-Mead optimization]</td>
            <td>Gradient information, Entropy, and Noise</td>
            <td>Shutter Speed and Gain</td>
            <td>Captures of a static scene with shutter and gain evenly spaced in the search space.</td>
        </tr>
        <tr>
            <td>Learned Camera Gain and Exposure Control for Improved Visual Feature Detection and Matching</td>
            <td>2021</td>
            <td>CNN</td>
            <td>Number of features and the number of inlier feature matches</td>
            <td>Shutter Speed and Gain</td>
            <td>Informed sampling using two cameras</td>
        </tr>
        <tr>
            <td>Auto-Exposure Algorithm for Enhanced Mobile Robot Localization in Challenging Light Conditions</td>
            <td>2022</td>
            <td>PRF transformations and Saturation Feedback</td>
            <td>Gradient Information</td>
            <td>Shutter Speed and Gain</td>
            <td>Two camera setup mounted on a custom three-axis (xy–yaw) motion table</td>
        </tr>
    </tbody>
</table>

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
