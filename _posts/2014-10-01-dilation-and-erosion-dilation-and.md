--- 
layout: post 
title: Morphological Operations
date: '2014-10-01T20:41:00.000-07:00'
author: Damian 
tags: [Image Processing]
modified_time: '2014-10-01T21:36:54.383-07:00'
thumbnail: http://1.bp.blogspot.com/-VaJ0pngO2-w/VCzD8fD-7fI/AAAAAAAAAUA/pVokgY96cw8/s72-c/dilation.jpg
blogger_id: tag:blogger.com, 1999:blog-2184234616367459774.post-184142332368133644
blogger_orig_url: https://damiandailisan.blogspot.com/2014/10/dilation-and-erosion-dilation-and.html
---

## Dilation and Erosion

Dilation and erosion was performed by hand on a graphing paper. The initial shapes and structuring elements are outlined in red, while the results are outlined in blue. In the structuring element, we first chose a pixel to use as the "origin". We reflect the image about this origin.  

For Dilation, we move the element around the shape, such that the origin remains in the shape. The combined outlines of the structuring element as it moves through the shape is the Dilation.  

{:refdef: style="text-align: center;"}
![](http://1.bp.blogspot.com/-VaJ0pngO2-w/VCzD8fD-7fI/AAAAAAAAAUA/pVokgY96cw8/s1600/dilation.jpg)\
*Dilation*
{: refdef}

For Erosion, we move the element around the shape, such that the element remains in the shape. The shape traced out by the origin is the Erosion of the shape due to the element.  

![](http://3.bp.blogspot.com/-XwA9vsw3aTA/VCzEft9MrkI/AAAAAAAAAUI/pmpoXv9Hg1I/s1600/erosion.jpg)\
*Erosion*
{: refdef}

In general, we can say that Dilation makes the shape bigger, while Erosion makes the shape smaller.

## Computer Simulation

![](http://2.bp.blogspot.com/-tGnOmzjs_SQ/VCy_9STV2kI/AAAAAAAAATY/02g_6jjYX-s/s1600/dilation%2Ball.png)\
*Dilation*
{: refdef}

![](http://3.bp.blogspot.com/-fsWZpXOKcU8/VCy_-6EazII/AAAAAAAAATg/paMRZRIDCog/s1600/erosion%2Ball.png)\
*Erosion*
{: refdef}

I performed Dilation and Erosion in Scilab using the IPD toolbox.
The results I obtained were consistent with my hand drawn Dilations and Erosions.  

## Cancer Cell Detection

The image file `Circles.jpg` was first binarized by setting a threshold value. The binarized image was then cleaned of small white dots using OpenImage, and holes in big circles were filled using CloseImage. The result is shown below (as a whole image, not the subimages). The image was segmented into subimages so that better thresholding can be applied on the different areas of the image, which may not be uniformly illuminated.

![](http://3.bp.blogspot.com/-gzDyeREQkKU/VCzNiWDgCOI/AAAAAAAAAUk/JPYymCG4USs/s1600/circ.png)

From this, we can measure the best estimate for "normal cells". We used SearchBlobs first, and then filtered the results to remove obviously large and small cells by area. Small cells would be less than 300 in area. Big cells will be above 600\. After this filtering we get

mean = 492\
stdev = 49.7
{: refdef}

From the mean and standard deviation, we used the same process to binarize `Circles with cancer.jpg`. Using SearchBlobs, we removed blobs which fit the best estimates for a normal cell. This leaves us with our abnormal cells, shown in the image below.

![](http://2.bp.blogspot.com/-gdg95Hez3-E/VCzJq2u_BaI/AAAAAAAAAUY/kVvwHM2r2DQ/s1600/cancer.png)
