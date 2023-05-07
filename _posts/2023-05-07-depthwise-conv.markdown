---
layout: post
title:  "Depthwise Separable Convolutions: An Experiment"
date:   2023-05-07 15:53:00 -0700
categories: convolution depthwise separable neural networks machine learning computer vision image processing
permalink: depthwise-conv
description: This blog discusses the benefits and limitations of depthwise 
             separable convolutions, a technique commonly used in CNNs to reduce
             computational complexity and memory requirements.
image:
  path: ../assets/images/depthwise-sep/depthwise-sep-2.png
  height: 1200
  width: 630
comments: true
---
Convolutional Neural Networks (CNNs) have been the go-to choice for many 
computer vision tasks such as image classification, object detection, and 
semantic segmentation. However, the size and computational complexity of CNNs 
can be a major bottleneck when it comes to deploying them on mobile and embedded
devices. To overcome this issue, researchers have proposed several techniques 
to reduce the size and computational complexity of CNNs without compromising 
their accuracy. One such technique is Depthwise Separable Convolutions.

MobileNet<sup>[1]</sup> was the first paper to propose a CNN architecture that is much faster 
as well as a smaller model that makes use Depthwise Separable convolution.

Depthwise Separable Convolutions are a type of convolutional layer that 
decomposes a standard convolution into two separate layers: a depthwise 
convolution and a pointwise convolution. The depthwise convolution applies a 
single filter to each input channel, creating a set of output channels. The 
pointwise convolution applies a 1x1 filter to combine the output channels from 
the depthwise convolution.

This separation of the convolution into two separate layers has several 
benefits. Firstly, the depthwise convolution reduces the number of computations 
by a factor of the input channel size. This is because each filter is only 
applied to one input channel instead of all input channels, resulting in a much 
smaller number of computations. Secondly, the pointwise convolution combines the 
output channels from the depthwise convolution, reducing the number of channels 
and therefore the number of parameters required to represent the layer.

To demonstrate the benefits of using Depthwise Separable Convolutions, let's 
consider a simple toy example. First lets import the required libraries.

<div class="overflow-table custom-highlight">
{% highlight python %}
import numpy as np
import time
{% endhighlight %}
</div>

Now lets suppose we have an input image of size $$[32X3X3]$$ and we want to 
apply a convolutional layer with 64 filters of size $$[3X3]$$. 
A standard convolutional layer would look as shown below. 

<div class="overflow-table custom-highlight">
{% highlight python %}
# Input image
x = np.random.rand(32, 32, 3)

# Standard convolutional layer
conv_std = np.random.rand(3, 3, 3, 64)
params_std = conv_std.size
start_time = time.time()
out_std = np.zeros((30, 30, 64))
for i in range(30):
    for j in range(30):
        out_std[i, j, :] = np.sum(x[i:i+3, j:j+3, :, np.newaxis] * conv_std, axis=(0,1,2))
std_time = time.time() - start_time
{% endhighlight %}
</div>

On the other hand, a Depthwise Separable Convolutional layer with the same 
number of filters and filter size would work as shown below.

<div class="overflow-table custom-highlight">
{% highlight python %}
# Depthwise separable convolutional layer
depthwise = np.random.rand(3, 3, 3, 1)
pointwise = np.random.rand(1, 1, 3, 64)
params_dw = depthwise.size + pointwise.size
start_time = time.time()
out_dw = np.zeros((30, 30, 64))
for i in range(30):
    for j in range(30):
        out_dw[i, j, :] = np.sum(x[i:i+3, j:j+3, :, np.newaxis] * depthwise, axis=(0,1,2))
out_dw = np.sum(out_dw[:, :, np.newaxis, :] * pointwise, axis=(0,1))
dw_time = time.time() - start_time
{% endhighlight %}
</div>

Now lets print out the statistics for both the convolutional layers.

<div class="overflow-table custom-highlight">
{% highlight python %}
print("Standard convolutional layer:")
print("Number of parameters:", params_std)
print("Computational complexity:", out_std.size)
print("Time taken:", std_time, "seconds")
print()
print("Depthwise separable convolutional layer:")
print("Number of parameters:", params_dw)
print("Computational complexity:", out_dw.size)
print("Time taken:", dw_time, "seconds")
{% endhighlight %}
</div>

The output of the above code is shown below.

<div class="overflow-table custom-highlight">
{% highlight bash %}
Standard convolutional layer:
Number of parameters: 1728
Computational complexity: 57600
Time taken: 0.032042503356933594 seconds

Depthwise separable convolutional layer:
Number of parameters: 219
Computational complexity: 192
Time taken: 0.017454147338867188 seconds
{% endhighlight %}
</div>

As is clearly evident, this is a significant reduction in both the number of 
parameters and computations required and hence the overall compute time as well.

In addition to reducing the size and computational complexity of CNNs, 
Depthwise Separable Convolutions can also improve their accuracy. This is 
because the separation of the convolution into two separate layers allows the 
network to learn more complex and diverse features. Furthermore, the depthwise 
convolution captures spatial information, while the pointwise convolution 
captures channel-wise interactions.

While depthwise separable convolutions have many advantages, there are also 
some disadvantages to using them in certain contexts:

1. Reduced expressiveness: Depthwise separable convolutions have fewer 
parameters than standard convolutions, which can result in a reduced ability to 
model complex features in some cases. 

2. Increased memory bandwidth: Depthwise separable convolutions require more 
memory bandwidth because they involve multiple operations (depthwise 
convolution followed by pointwise convolution) on the same data. This can be 
problematic on devices with limited memory or when processing large datasets.

3. Increased sensitivity to initialization: Because depthwise separable 
convolutions have fewer parameters, they can be more sensitive to 
initialization. Poor initialization can lead to vanishing or exploding 
gradients, which can degrade performance.

In conclusion, Depthwise Separable Convolutions are an effective technique for 
reducing the size and computational complexity of CNNs. They achieve this by 
decomposing a standard convolution into two separate layers: a depthwise 
convolution and a pointwise convolution. This technique is particularly useful 
for deploying CNNs on mobile and embedded devices where computational resources 
are limited.

#### References

[1] Howard, Andrew G., et al. ["Mobilenets: Efficient convolutional neural networks for mobile vision applications."](https://arxiv.org/pdf/1704.04861.pdf%EF%BC%89) arXiv preprint arXiv:1704.04861 (2017).