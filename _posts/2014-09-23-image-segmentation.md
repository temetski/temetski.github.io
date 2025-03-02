---
layout: post
title: "Image Segmentation"
tags: [Python, Image Processing]
time: "2014-09-23T23:23:04.745-08:00"
toc:
  sidebar: true
---

So far we have learned to analyze ideal images of shapes, which we have set up and know exactly what they are. Usually, these are also black and white, which makes it easy for our programs to distinguish between "something and nothing". These idealized cases are far from reality. Images which we have to process are usually colored, and we have no idea how to get what we need. An example would be trying to perform some analysis on the red part of this Gundam model kit.

[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhlCcr96o7-NxU_rUCsbkpBQioW_W_d68-u7LBKqqy890mLXyVd5mQUEoKXDApdZf3r1j2KmytArh5Ua8jyPTzdayBOxPOUzNBACTJahyijjiHAXiQEtKfDD_UCa1Y2NFSBl-cAmj8PbfZR/s1600/gundam.jpg)

How do we do that??? Well, we first need to teach our program to separate the red from the rest of the image. The image contains RGB values for each pixel. We can guess that the red parts of the gundam satisfy certain values of R, G and B. We could have a sample Region of Interest (ROI), however, that would probably just be a finite range of shades. Since we want to automate getting all of the red, it would be pointless to have our ROI as the whole image.

The solution comes in the representation of RGB in the Normalized Chromacity Coordinates. The idea is that each pixel will have,

$$
\begin{aligned}
q &= \frac{Q}{(R+G+B)} \text{ where q,Q are rgb and RGB}\\
r + g + b &= 1
\end{aligned}
$$

Because of this, we can represent the color of a pixel in terms of only two variables, $$r$$ and $$g$$. This greatly simplifies how we search for color. Our ROI will now satisfy only a small region in the NCC.

From the NCC representation of the ROI, we can use 2 methods to segment the image.

## Parametric

We can take the distributions of the r ang g values in the ROI. This represents the distribution of the r and g values that represent the ROI, and other parts of our image. We fit Gaussian functions to these histograms, and we will use the probibilities of the Gaussian functions in deciding if a pixel is part of our target color. Of course, the closer the r ang g value of the pixel is, the more chances that it is going to be used. The result is shown below; you can see that it is quite noisy. This is because being a probabilistic approach, some pixels are not included even if they are red.

[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3VLvX31rtSrNmP_RM5EG4ko-Hqnr_YSkh6zRI9JZnvqo-UV6Xl3q8jmJhKpJUdzsv4X_7SOx_Q9Cp2xkTcmosSIGVRzwdWRFr3sSByE3IijqgvQ8_HuS1ouKwQbAGSt6gdit8EULiUpfH/s1600/rg.jpg)

[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVZvZ9pWbkCHG74LYhCim0DpMv0NYZtPiOkz2Kukw-ZCDDBBo2OBlzvrmbASbWymQyDCRq_5MrdJCK28bAaFJKFitjWQBfudmdehwz-raxsIWWVrw-m7o1LBFVQgI3xSN8hQWStPtrtxRL/s1600/parametric.jpg)

## Histogram Backprojection

An alternative approach is histogram backprojection. Instead of taking probabilities, we instead look at the 2D histogram in the NCC.

[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQgLR1dLqtaiQoFv3HZjYU_iJzZmFrQASkulD9b6msCL-YIWkCXHIAucON8ypsIEs_7SNfU5u6HmYYWxMLqim1tweV5kE7nB9pukUEpkTqnKjpP-U4UGv5g6vGMmdDyYGyZuOuFVFLeDBM/s1600/hist.jpg)

All of the non black points represent viable colors that should match our desired color. In the parametric method, we assume Gaussian functions for both r and g. This is the same as saying that the 2D histogram is an ellipse. This isn't the case as we can see now. The 2D histogram is more of a slanted ellipse.

In backprojection, we simply take all of the r and g values that are not zero, and use the pixels that have the same r and g values. The result is a much more acceptable segmentation shown below.

[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimTkCzdxqfOrgISzwwf7FPg6w0A6lsgyrybYlL01Gtzp_h6M4SNzfE-DILWiVNnXCub0QAHtYSyLr5Duo3ixDQ5x8_giktFR44a4kNOsqQyuqLY5gDJPHVWSj4AyYa0_U0kAuS5nq_qsti/s1600/back.jpg)

This is a fun activity, especially after seeing the color separation (by Image segmentation.) I give myself 10/10
