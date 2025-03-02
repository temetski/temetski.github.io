---
layout: post
title: "Project: Car Tracking for velocity measurement"
tags: [Python, Image Processing]
time: "2014-11-17T23:23:04.745-08:00"
---

For my project, I want to try tracking cars in a video to determine velocities of vehicle on the road.
Just a simple code with a rough estimate of velocity is what I need for now, I don't need to match velocities to specific cars yet.
But first, I need to be able to track an object across a video, which is simply a series of still images.

## Tracking based on Image Segmentation

![](http://1.bp.blogspot.com/-37kpVVTX3VA/VFg19D7w6XI/AAAAAAAAAXA/gd51pcGPTLU/s1600/0092.png)

We can start things of by tracking a ping-pong ball undergoing projectile motion to calculate the acceleration due to gravity.
We simply converted the video to a series of images using ffmpeg as a decoder.
Then we took a set of images which corresponds to one projectile motion.

![](http://3.bp.blogspot.com/-EExvBGS2Xws/VFg19C1VTlI/AAAAAAAAAW8/ud53uNSHDWw/s1600/roi.jpg)

We select the ROI by color, just crop the pingpong ball. And then use
Image segmentation using [histogram back
projection]({{ site.baseurl }}{% post_url 2014-09-23-image-segmentation %}).

![](http://2.bp.blogspot.com/-712CpDx7Hns/VFg19AFXHbI/AAAAAAAAAXE/DvmFW3yeizE/s1600/back.jpg)

Now we have a collection of points which represent the ball. We can
improve the quality of our collection of points using morphological
operations to clean it as well. We can get the centroid using this
following snippet of code:

{% highlight python %}
IsCalculated = CreateFeatureStruct(%f);
IsCalculated.Centroid = %t;
C = AnalyzeBlobs(im, IsCalculated);
Centroid = C(1).Centroid;
y = [y Centroid(2)];
x = [x Centroid(1)];
{% endhighlight %}

Doing this for the rest of the images, we will get a collection of x and y positions, which we can plot using excel.
Of course, these x and y positions need to be scaled properly, using

{% highlight python %}
yscale = .05/20 // meters to pixels
xscale = .05/14 // meters to pixels
Y = -yscale*(y-240);
X = xscale*(x);
{% endhighlight %}

If we fit a second order polynomial to the y positions and time, we
should get

![](http://1.bp.blogspot.com/-84kfWpIKTwA/VFg6UgfC0HI/AAAAAAAAAXY/eew52qeaFlM/s1600/color.png)

The term $-4.8871$ is actually $-\frac{g}{2}$.
Hence, we have $g=9.77\,\frac{m}{s^2}$.

## Tracking Based on Image Difference

Vehicles will not always have the same color on the road.
The method of image segmentation is good, but we need something more robust if we want to be able to make a generic car velocity estimator just by video
processing.
A possible alternative method is using image difference.
We need an image of just the background.

![](http://4.bp.blogspot.com/-OgHTmJvfy7M/VFg7v5kP7oI/AAAAAAAAAXk/smnZ2krxTJ8/s1600/bg.jpg)

Then we take the difference of every frame with this frame. To binarize
the difference , we set a threshold value, which will determine if
pixels in the difference will be black or white.

We'll get another blob, from which we can get its position using
centroids. We can get the following graph below:

![](http://1.bp.blogspot.com/-0eZgnGVw4lQ/VGq-yLZQyFI/AAAAAAAAAX0/zWEYFrxgEyk/s1600/graph.png)

The term $-4.8871$ is actually $-\frac{g}{2}$.
Hence, we have $g=9.78\,\frac{m}{s^2}$. This is
a similar result as with using image segmentation by color. We are now
ready to track car
videos.

## Tracking cars from a video

![](http://2.bp.blogspot.com/-X21Eu_daJbo/VGq-6Rx6FKI/AAAAAAAAAYA/INZwQLRsO44/s1600/fig1.jpg)

A video frame would look like the image shown above.
Since we are working with a video captured by a different person, we do not have a background that we can readily use as a reference.
Instead, we utilize the difference of subsequent frames, and hope to find the outlines of the vehicles on the road.
However, as shown in the next two figures, this is not as straightforward as the previous methodology of subtracting a reference background.

![](http://2.bp.blogspot.com/-tcvLrTyL9sU/VGq-6Az9ewI/AAAAAAAAAX8/CHPBWn1oD6s/s1600/diff.jpg)

![](http://3.bp.blogspot.com/-1kz2FFv-Vn4/VGq-7oOHZuI/AAAAAAAAAYU/HZjj5vt3GC0/s1600/imdiff.png)

![tracks](http://2.bp.blogspot.com/-2nE8WvBfkb4/VGq-8QTDe6I/AAAAAAAAAYc/4xYRBU4GlhI/s1600/std.jpg)

![](http://3.bp.blogspot.com/-QaViLEdy55E/VGq-6b2dsMI/AAAAAAAAAYE/CnSJQJ5PM_g/s1600/histogram.png)
