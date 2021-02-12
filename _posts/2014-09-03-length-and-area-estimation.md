---
layout: post
title: Length and Area Estimation
date: '2014-09-03T08:57:00.000-07:00'
author: Damian
tags: [Image Processing]
modified_time: '2014-09-03T08:57:15.359-07:00'
thumbnail: http://3.bp.blogspot.com/-ZLAVJcMVlt8/VAVHZ2ex_aI/AAAAAAAAALE/qRi0xUVQq2Q/s72-c/square.bmp
blogger_id: tag:blogger.com,1999:blog-2184234616367459774.post-4382617168451981428
blogger_orig_url: https://damiandailisan.blogspot.com/2014/09/length-and-area-estimation.html
---

Measurements play a huge role in any scientific endeavor.
But sometimes, measuring objects is a physically difficult task.
Those objects may be too small, or too big, or we may simply not have any analytic solution to the measurement of that
object.
Luckily for us, as long as we have an image of that object with a corresponding scale, we can easily get a good enough
measurement of that object.

For example, we want to measure the area of a square image:

{:refdef: style="text-align: center;"}
![a square image](http://3.bp.blogspot.com/-ZLAVJcMVlt8/VAVHZ2ex_aI/AAAAAAAAALE/qRi0xUVQq2Q/s1600/square.bmp)
{: refdef}

What we have is a 300x300 square surrounded by a black border.
Since we know that this *should* have an area of 90000, we should be able to recover that number with our code.

Our code is based on [Green's Theorem](http://en.wikipedia.org/wiki/Green's_theorem), which allows us to measure the area of
a region in a 2d plane, provided that we know the contour of that region.
Using the `canny` edge detection algorithm
in scilab, and applying Green's Theorem:

~~~matlab
im = imread('square.bmp')
im_edge = edge(im, 'canny');
imshow(im_edge);
[y, x] = find(im_edge==%t);
xo = floor(mean(x)); yo = floor(mean(y));
x = x - xo;
y = y - yo;
theta = atan(y, x);

[B, k] = gsort(theta, 'g', 'i');
x = x(k); y = y(k);

//GREENS THEOREM
A = 0
for i=1:length(x)-1
    A = A + 0.5*(x(i)*y(i+1) - y(i)*x(i+1))
end
~~~

We find that the area given by the code is 89922 square pixels!
That's about 99.913% accuracy, not bad for a line of code that takes milli(maybe even micro)-seconds to
run.

Now that we have our code, let's test it on a real live "object". 
The compass square! (Otherwise known as North, East, West, Timog Ave.)

![map](http://1.bp.blogspot.com/-J73JwnaQjbk/VAc2n-_iW4I/AAAAAAAAALo/_oED1-smKdw/s1600/map.png)
{: refdef}

We cannot directly use the image from Google Maps. So let us turn it into a
black and white image, with our area of interest as a white box.

![mask](http://4.bp.blogspot.com/-JyfsEWA6E_I/VAc2nuV6dcI/AAAAAAAAALk/YSS0YnjuHqc/s1600/map.bmp)
{: refdef}

Note that 108 pixels : 500 meters.

Running our code gives us 4380326.2 square meters. 
Analytical estimates obtained by measuring the side of the square in GIMP gives us 4282600.3 square meters.
That's a difference of roughly 2.4%, which may be due to the shape not being a perfect square. Using another image processing software, `ImageJ`, we get 4579482.9 square meters for the same image. Its a little off, but still within less than 5% different with our own measurements, so I think that our code was successful!
        
The only thing "difficult" about the experiment is the creation of the black and
white image. I think something like a polygon tool from `ImageJ` would be better in creating the bounded
area.
