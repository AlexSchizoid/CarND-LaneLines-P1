#**Finding Lane Lines on the Road** 

##First Udacity Self-Driving Nanodegree project

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images_output/blur_gray_solidWhiteCurve.jpg
[image3]: ./test_images_output/blur_gray_solidYellowCurve.jpg
[image4]: ./test_images_output/canny_solidWhiteCurve.jpg
[image5]: ./test_images_output/canny_solidYellowCurve.jpg
[image6]: ./test_images_output/maskededges_solidWhiteCurve.jpg
[image7]: ./test_images_output/maskededges_solidYellowCurve.jpg
[image8]: ./test_images_output/lines_edges_solidWhiteCurve.jpg
[image9]: ./test_images_output/lines_edges_solidYellowCurve.jpg
[image10]: ./test_images_output/lines_on_image_solidWhiteCurve.jpg
[image11]: ./test_images_output/lines_on_image_solidYellowCurve.jpg

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline i've developed is separated into 5 different stages. I'm going to go over them one by one.
The first thing we do is greyscale the image, while also adding a little blur on it. This smoothes out gradients and should make our lane lines more obvious do detect. After this stage the output looks like this:

![alt text][image2]
![alt text][image3]

In the second stage we run grayscaled image through the canny edge detection algorithm. We cycle through different threshold values for the canny edge detection and we empirically select values 50,150 - this seems to provide the best output in my opinion. An example output from this stage looks like this:

![alt text][image4]
![alt text][image5]

In the third stage we define a region of interest, described by a four-sided polygon, which will select the region which containes the lane lines, while masking everything else. The regions vertices use certain ratios applied on the dimensions of the full image. This allows us to use different resolution images, meaning it will work on something other than the current test images. An example output from this stage looks like this:

![alt text][image6]
![alt text][image7]

In the fourth stage we input the regions discoverd previously to the Hough Transform algorithm. This algorithm provides a series of lines detected in the previous stages. The inputs to the Hough Transform API were found empircally through trial and error. Using these detected lines we can extrapolate one line for each lane, left and right. What i did was basically calculate the slopes and intercepts of each line, dividing left and right lane lines according to the sign of the slope. By averaging the coordinates and slope/intercept we could get a dominant line, on for each lane. This is the line we then proceed to draw on black canvas. 

I also decided to reject certain outliers that fall out of an acceptable slope range. This can happen when running the pipeline on the test videos. In this case what i do is maintain an average of the last 10 frames, and when the slope is unnaceptable just draw an average of the lines detected in the previous 10 frames. Here is an example output of this stage. The lines drawn on the edges detected previously cand tell us more easily how well we did.

![alt text][image8]
![alt text][image9]

The fifth and final stage of the pipeline draws the lines we extrapolated in the previous stage on the original image. Here are examples:

![alt text][image10]
![alt text][image11]

###2. Identify potential shortcomings with your current pipeline

One of the potentail shortcomings i believe my current design has is a limitation when there are a lot of shadows or very bright lighting. Even if we greyscale the image it might be difficult to detect certain lanes.

Another shortcoming is the fact that if the radius of the curve changes rapidly, just drawing a line might not be good enough. The pipeline needs to be able to draw nonlinear lanes.

When I get to the advanced lane detection i'd like to change the code to be object oriented.

###3. Suggest possible improvements to your pipeline

Instead of greyscale I should maybe convert the image to HSV space. This should allow me more easily separate the lanes by color, by playing with the parameters of the HSV space.

