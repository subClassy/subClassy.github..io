---
layout: post
title:  "Video Compression using Generative Models"
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

I few weeks ago, I wrote a blog post on 
[H.265 vs AV1: The Battle for Efficient Video Compression!](https://subclassy.github.io/compression).
Here I talked about the differences between H.265 and AV1 video compression 
standards and some experiments I did to compare them. In this post, I will
introduce the use of deep generative models for video compression and review 
some recent papers that apply this technique to various applications. Video 
compression is a fundamental problem in computer vision and multimedia, as it 
aims to reduce the storage and transmission costs of visual data while 
preserving its quality and information content. Traditional video compression 
methods rely on hand-crafted transforms, quantization schemes, and entropy 
coding algorithms that are designed based on human perception and signal 
processing principles. However, these methods have limitations in handling 
complex and diverse visual data, especially at low bitrates.

<img class="chatgpt-meme-drake" align="right" src="../assets/images/deep-video-compression/gptvsai.png" alt="chatgpt-meme" width="300"/>

Generative AI models have taken the world by storm in the recent history with
GPT based models, LLMs etc. However, generative models, such as variational 
autoencoders (VAEs) and generative adversarial networks (GANs), offer more than
just allowing you to talk to ChatGPT when you are alone!!


These models offer a new paradigm for video compression, as they can learn 
data-driven representations that capture the underlying structure and 
distribution of the visual data. Moreover, they can leverage powerful neural 
networks to perform nonlinear transforms, quantization, and entropy coding in 
an end-to-end manner. The main idea of deep generative models for video 
compression is to encode the input video into a compact latent representation 
that can be decoded by a generative model to reconstruct the original or a 
high-quality approximation of it. The latent representation can be further 
discretized and entropy coded to produce a compressed bitstream that can be 
transmitted or stored.

#### Research Papers

In this section, I will review four papers that have been published in the last
few years. I have not chosen these papers as just the state-of-the-art, but I 
have chosen this papers because they each present a unique and different approach
to the same problem. 

<subheading><h5>
<a href="https://openaccess.thecvf.com/content_CVPR_2019/papers/Lu_DVC_An_End-To-End_Deep_Video_Compression_Framework_CVPR_2019_paper.pdf">[1]</a>
<mark>DVC: An End-to-end Deep Video Compression Framework</mark>
</h5></subheading>

This paper by Lu et al., is the first paper to propose an end-to-end video 
compression deep model that jointly optimizes all the components for video 
compression. Simply put, they take the traditional video compression pipeline 
and replace each component with a neural network.

Specifically, learning-based optical flow estimation is utilized to obtain 
motion information and reconstruct current frames. Then two auto-encoder style 
neural networks are employed to compress the corresponding motion and residual 
information. 

The model consists of several key components: motion estimation and compression,
motion compensation, transform, quantization and inverse transform, entropy 
coding, and frame reconstruction. To make it simple to understand, I have taken
frames from the paper and annotated the image with the corresponding component.

| ![lu_2019](../assets/images/deep-video-compression/lu_2019.png "Figure A.1") | 
|:--:| 
| *Figure A.1 [1]* |

1. <strong>Motion estimation and Compression</strong> - a CNN model is used to 
estimate optical flow, which is considered as motion information. An motion 
vector encoder-decoder network is proposed to compress and decode optical flow 
values. 
2. <strong>Motion compensation</strong> - this network is designed to obtain the
predicted frame based on optical flow. 
3. <strong>Residual Encoding</strong> - The residual information between the 
original frame and predicted frame is encoded by a residual encoder network. A 
highly non-linear neural network is used to transform residuals to corresponding
latent representation. 
4. <strong>Quantization</strong> - The latent representations are required to be
quantized before entropy coding. 
5. <strong>Bit Rate Estimation Net</strong> - To estimate bit rates of generated
latent representations, CNNs are used to obtain probability distribution of each 
symbol.

Finally, frame reconstruction is performed by adding predicted frame and 
reconstructed residual.

This paper is interesting because it does not use an explicit Generative Model,
but the architecture is similar to an auto-encoder in various stages of the 
pipeline. This paper has become the guiding light for many other papers in this
area which improve upon this basic architecture.

<subheading><h5>
<a href="https://proceedings.neurips.cc/paper/2019/file/f1ea154c843f7cf3677db7ce922a2d17-Paper.pdf">[2]</a>
<mark>Deep Generative Video Compression</mark>
</h5></subheading>

This paper comes from Disney Research. In this paper Han et al., builds upon 
the work done in Variation Auto-Encoders (VAE) for neural image compression and 
use it to model sequential data and jointly learn to transform the original 
sequence into a lower-dimensional representation as well as to discretize and 
entropy code this representation according to predictions of the sequential VAE.

This paper was the first to propose a core generative approach for video 
compression. If you want to get into the maths of it all, I would suggest you
to read the paper. Shown below is the high-level operational diagram of the 
proposed compression codec. A video segment is encoded into per-frame latent 
variables $$z_t$$ and also into a per-segment global state $$f$$ (which I have
omitted from the diagram for simplicity) using a VAE architecture. The latent 
variables are then quantized and arithmetically encoded into binary according 
to the respective prior models. To recover an approximation to the original 
video, the latent variables are arithmetically decoded from the binary and 
passed through the neural decoder.

| ![han_2019](../assets/images/deep-video-compression/han_2019.png "Figure A.2") | 
|:--:| 
| *Figure A.2 [2]* |

The most interesting part of the paper is the use of a temporally-conditioned
prior distribution parameterized by the VAE to code the latent variables 
associated with each frame. This is what separates this paper from full compression
algorithms.

The proposed architecture also splits up the latent code into global and local 
variables which I have removed from the diagram. The global variables are 
inferred from all video frames in a sequence and may contain global information,
while the local variables are only inferred from a single frame. 

This paper is a very good read and I would recommend it to anyone who is diving
into the world of generative models.

<subheading><h5>
<a href="https://openaccess.thecvf.com/content/ICCV2021/papers/Tian_Self-Conditioned_Probabilistic_Learning_of_Video_Rescaling_ICCV_2021_paper.pdf">[3]</a>
<mark>Self-Conditioned Probabilistic Learning of Video Rescaling</mark>
</h5></subheading>

This paper by Tian et al., is a very interesting paper becauses this is a 
video rescaling algorithm instead of video compression. However, they propose
an application of their algorithm to video compression that can be trained 
end-to-end.

The authors propose a self-conditioned probabilistic framework, which 
simultaneously learns paired downscaling and upscaling procedures. The rescaling
framework consists of three components: 
1. a frequency analyzer,
2. a spatial-temporal prior network (STP-Net), and 
3. a frequency synthesizer. 

During the downscaling procedure, the frequency analyzer converts a 
high-resolution video into low-frequency and high-frequency components. 
The low-frequency component is quantized to produce a low-resolution video, 
while the high-frequency component is discarded. During the upscaling procedure,
the STP-Net predicts the probability density function of the high-frequency 
component conditioned on the low-resolution video - $$p(f_h|x_l)$$, by modelling 
a Gaussian Mixtures Model (GMM).

A sample of the high-frequency component is then drawn from this distribution 
and combined with the low-resolution video using the frequency synthesizer to 
reconstruct the original high-resolution video. 

In terms of video compression, the paper claims that compressing the down-scaled
videos using a standard video codec like H.265 can achieve a better compression 
ratio than compressing the original high-resolution videos. But since, video
codecs are non-differential, the authors propose a differentiable surrogate
using a DNN to approximate the H.265 codec. This is shown in the image below.

| ![tian_2021](../assets/images/deep-video-compression/tian_2021.png "Figure A.3") | 
|:--:| 
| *Figure A.3 [3]* |

<subheading><h5>
<a href="https://arxiv.org/pdf/2207.05315.pdf">[4]</a>
<mark>CANF-VC: Conditional Augmented Normalizing Flows for Video Compression</mark>
</h5></subheading>

This paper by Ho et al., proposes a video compression framework using 
Conditional Augmented Normalizing Flows (CANFs) which are an extension of 
Normalizing Flows (NFs) to the conditional setting. Normalizing flows are 
generative models that produce tractable distributions by transforming a simple 
probability distribution into a more complex and flexible one through a sequence
of invertible and differentiable functions. These functions must have analytical
inverses and follow the rule for change of variables. By repeatedly applying the
rule for change of variables, the initial density ‘flows’ through the sequence 
of invertible mappings. If you want to learn more about Normalizing Flows, I 
recommend you to watch this [video](https://youtu.be/3KUvxIOJD0k) from the 
University of Waterloo.

The authors suggest the use of Augmented Normalizing Flows (ANFs) since its a 
more generalized generative model and has superior expressive power to VAEs. They
draw this idea from ANFIC[6] which uses ANFs for image compression.

| ![ho_2022](../assets/images/deep-video-compression/ho_2022.png "Figure A.4") | 
|:--:| 
| *Figure A.4 [4]* |

The architecture of this approach has two major components:
1. CANF inter-frame coder: which encodes a video frame conditionally, give the
motion compensated frame - $$p(x_t |  x_c)$$
2. CANF motion coder: which shares a similar architecture to the inter-frame 
coder, with the coding frame replaced by an optical flow map and the 
motion-compensated frame replaced by an extrapolated flow map. The flow map is 
estimated by PWC-Net and serves to warp the reference frame, with the warped 
result further enhanced by a motion compensation network.

The decoding process updates the motion-compensated frame successively to 
reconstruct the target frame. The quantized latent is then recovered and decoded
to update the motion-compensated frame, which is then encoded conditionally 
based on its previous state in order to update the latent. This process is 
repeated until the reconstructed version of the target frame is achieved.

This is a very well written paper and the above summary does not justify the 
complexity of the paper. I would recommend you to read the paper if you are 
interested aboyut this approach.


#### References

[1] Lu, Guo, et al. "[Dvc: An end-to-end deep video compression framework.](https://openaccess.thecvf.com/content_CVPR_2019/papers/Lu_DVC_An_End-To-End_Deep_Video_Compression_Framework_CVPR_2019_paper.pdf)" 
Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2019.

[2] Lombardo, Salvator, et al. "[Deep generative video compression.](https://proceedings.neurips.cc/paper/2019/file/f1ea154c843f7cf3677db7ce922a2d17-Paper.pdf)" Advances in Neural Information Processing Systems 32 (2019).

[3] Tian, Yuan, et al. "[Self-conditioned probabilistic learning of video rescaling.](https://openaccess.thecvf.com/content/ICCV2021/papers/Tian_Self-Conditioned_Probabilistic_Learning_of_Video_Rescaling_ICCV_2021_paper.pdf)" 
Proceedings of the IEEE/CVF international conference on computer vision. 2021.

[4] Ho, Yung-Han, et al. "[Canf-vc: Conditional augmented normalizing flows for video compression.](https://arxiv.org/pdf/2207.05315.pdf)" 
Computer Vision–ECCV 2022: 17th European Conference, Tel Aviv, Israel, October 23–27, 2022, Proceedings, Part XVI. Cham: Springer Nature Switzerland, 2022.

[5] Ho, Yung-Han, et al. "[Anfic: Image compression using augmented normalizing flows.](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9623351)" IEEE Open Journal of Circuits and Systems 2 (2021): 613-626.