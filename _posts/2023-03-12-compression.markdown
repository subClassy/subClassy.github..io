---
layout: post
title:  "H.265 vs AV1: The Battle for Efficient Video Compression!"
date:   2023-03-12 10:48:00 -0700
categories: compression h265 av1
permalink: compression
description: Discover the differences between H.265 and AV1 video compression 
            standards and their impact on visual quality, hardware support, and 
            licensing in our in-depth comparison.
image:
  path: ../assets/images/compression/jj-ying-WmnsGyaFnCQ-unsplash.jpg
  height: 1200
  width: 630
---
Currently, the amount of video traffic is growing exponentially and so is the 
availability of high-resolution videos from mobile devices. However, this also 
increases the storage burden and bandwidth requirements to transfer these 
videos. As presented in 
[Back to basics: GOPs explained](https://aws.amazon.com/blogs/media/part-1-back-to-basics-gops-explained/) 
we require an approximately 12 Gbps bitrate to carry uncompressed 4K video. So 
video compression techniques are crucial for video transfer, processing, and 
storage. Two popular compression standards that have been widely adopted in 
recent years are H.265 (HEVC) and AV1.

H.265, also known as High Efficiency Video Coding (HEVC), is a video compression 
standard developed by the Joint Collaborative Team on Video Coding (JCT-VC), a 
collaboration between the International Telecommunication Union (ITU) and the 
Moving Picture Experts Group (MPEG). H.265 offers significant improvements over 
its predecessor, H.264, including higher compression efficiency, better visual 
quality, and support for 4K and 8K resolutions.

AV1, on the other hand, is a royalty-free, open-source video compression 
standard developed by the Alliance for Open Media (AOMedia), a consortium of 
technology companies including Google, Mozilla, Netflix, and Amazon. AV1 was 
designed to provide even better compression efficiency than H.265, while also 
addressing some of the patent and licensing issues associated with previous 
video compression standards.

In this article, I will compare these two algorithms across metrics like 
compression efficiency, speed, reconstruction quality, etc, by testing 
them out on a custom video dataset.

#### Preparation

For the purpose of this blog, I recorded 15 videos of 60 frames each in HD 
resolution (1920x1080). The total size of this dataset was $$148.7MB$$. I use this
same dataset across all my experiments below.

To setup the compression systems, I use Python along with FFmpeg with H.265 
and AV1 codec activated. I used this guide - [FFmpeg Compilation Guide](https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu)
to compile FFmpeg on my system. The only thing you need to be careful about is 
to follow the steps under *libx265* which is the H.265 encoder and *libaom* which
is the AV1 encoder. 

Once you compile FFmpeg, you can use [scikit-video](http://www.scikit-video.org/stable/io.html)
to read and write videos in your Python code using $$FFmpegReader$$ and $$FFmpegWriter$$.

Now lets dive into the experiments.

#### Compression

The most obvious experiment to do is to check the compression ration offered by
the two standards. Now the thing to remember here is that both of these
compression standards offer something called *"Constant Rate Factor (CRF)"*.
CRF allows the user to tradeoff between quality and compression ratio. Its quite
obvious that, the better compression ratio you have - the poor the reconstruction would
be. For this experiment I will use multiple values of CRF for both H.265 and AV1
and we can observe the trend for different CRF values.

Shown below is the code to compress a video using H.265 standard. The code had
two major components - creation of the writer and writing of individual frames.
In the function *open_writer*, I pass the width and height of the frames that I 
want to compress. Along with these I also pass in $$q$$, which is the CRF value
that I want to use. Inside this method, I create a *x265* config where I set the
CRF value, which ultimately becomes part of the output dictionary that is required 
by the $$FFmpegWriter$$. In the second function *write_multi_frames*, I write a
batch of frames to the above created writer.

<div class="overflow-table custom-highlight">
{% highlight Python %}
def open_writer(w, h, q):
    file_random_name = str(time.time())
    video_name = "./tmp/outputvideo/" + file_random_name + "_H265.mkv"
    x265_config = "crf={}:".format(q)
    out_dict = {
        "-s":str(w)+"x"+str(h),
        "-pix_fmt":"yuv444p",
        "-c:v":"libx265",
        "-x265-params":x265_config
    }
    rgb_yuv_writer = skvideo.io.FFmpegWriter(
        video_name,
        inputdict = {
            "-s":str(w)+"x"+str(h),
            "-pix_fmt":"rgb24",
        },
        outputdict = out_dict,
        verbosity = 1)

def write_multi_frames(input, rgb_yuv_writer):
    bt, c, h, w = input.size()
    frames = output.permute(0, 2, 3, 1)
    frames = frames.cpu().numpy().astype(np.uint8)
    for i in range(bt):
        rgb_yuv_writer.writeFrame(frames[i, :, :, :])
{% endhighlight %}
</div>

For AV1, the procedure is quite similar. The only function that changes is the
*open_writer* where I change the encoder to AV1.

<div class="overflow-table custom-highlight">
{% highlight Python %}
def open_writer(w, h, q):
    file_random_name = str(time.time())
    video_name = "./tmp/outputvideo/" + file_random_name + "_AV1.mkv"
    av1_config = str(q)
    out_dict = {
        "-s":str(w)+"x"+str(h),
        "-pix_fmt":"yuv444p",
        "-c:v":"libaom-av1", 
        "-crf":av1_config
    }
    rgb_yuv_writter = skvideo.io.FFmpegWriter(
        video_name,
        inputdict = {
            "-s":str(w)+"x"+str(h),
            "-pix_fmt":"rgb24",
        },
        outputdict = out_dict,
        verbosity = 1)
{% endhighlight %}
</div>

In the image below I plot the graph for compressed size vs CRF value for both 
the compression standards. From the graph, we can see that there is not a lot 
of difference between the compression ratio offered by the two standards for the CRF
value. 

| ![Compressed Size vs CRF](../assets/images/compression/compress_vs_crf.png "Figure A.1") | 
|:--:| 
| *Figure A.1* |

#### Visual Quality

In the previous part we compared the compression ratio at different CRF values.
However, in reality this is an unfair comparison since two different standards
might provide different visual quality at the same CRF. By visual quality,
we try understand the closeness of the uncompressed video to the original video.
To investigate this, we use two metrics - *Peak Signal to Noise Ratio (PSNR)* and
*Structural Similarity (SSIM)*. These are two industry standard metrics that have 
are used to compare two images. So for this experiment, we compress and de-compress
our video, and then compare the reconstructed frames to the original ones and calculate 
PSNR and SSIM.

| ![Image Quality vs CRF](../assets/images/compression/image_quality.png "Figure A.2") | 
|:--:| 
| *Figure A.2* |

In this figure, we can see the AV1 provides a better visual quality for CRF values.
In fact, if we observer carefully, we get the same visual quality from AV1 and *CRF=27*
that we get from H.265 at *CRF=21*. If we put this context with our compression ratio, 
this would mean that AV1 can offer higher compression ratio at similar visual quality as H.265.

#### Processing Times

The next important factor to compare these algorithms is the time they need to encode and
decode. Currently when we are using FFmpeg, we are essentially encoding using software without 
any hardware acceleration. While both H.265 and AV1 can be decoded using software-based 
solutions, hardware support is also an important factor to consider. H.265 has been widely 
adopted by hardware manufacturers, and many modern devices, including smartphones, 
tablets, and smart TVs, include hardware acceleration for H.265 decoding. AV1, on the 
other hand, is still relatively new, and hardware support is currently limited. 

Hence for the purpose of this discussion, we will only consider the software based encoding.

Also, since we established that we get similar quality of image at *CRF=27* for AV1 and 
*CRF=21* for H.265, we will restrict our discussion to these specific configurations. H.265 takes 2.83s
on average to encode the entire data and takes 1.74s to decode the same. AV1 on the other hand
takes a whopping 445.799s to encode while 2.865s to decode. Ahh! so heres the catch,
AV1 is so good but it takes so much time to process. In any real-time system, this would be
a dealbreaker.

However, like any good algorithm, AV1 provides some dials that we can tune to achieve
better computation speeds at some loss of quality. I would suggest to look into
switches like <mark>-cpu-used</mark>, <mark>-usage realtime</mark> and <mark>-row-mt 1</mark>
from [AV1 - Controlling Speed/Quality](https://trac.ffmpeg.org/wiki/Encode/AV1#ControllingSpeedQuality)
to improve these processing times.

#### Licensing and Royalties

From a business perspective, one of the key advantages of AV1 over H.265 is its 
royalty-free status. H.265 is subject to licensing fees, which can be a 
significant barrier to entry for some companies. AV1, on the other hand, is 
completely free to use and is not subject to any licensing fees or royalties.

#### Conclusion

In conclusion, both H.265 and AV1 offer significant improvements over previous 
compression standards, and both have their own unique strengths and weaknesses. 
While H.265 is widely supported and offers hardware support but it comes with a licensing issue.
AV1 has the potential to surpass H.265 in terms of the comprrssion compression 
efficiency and visual quality, and its royalty-free status makes it an attractive option. 
