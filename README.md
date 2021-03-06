# **Finding Lane Lines on the Road** 
---
(For an improved version, see: [Advanced Lane Detection](https://github.com/NachoCM/CarND-Advanced-Lane-Lines))

**Finding Lane Lines on the Road**

The goal of this project was  to make a pipeline that finds lane lines on the road.



[//]: # (Image References)

[image_low_slope]: ./examples/low_slope_example.jpg "Low slope lines"
[image_center]: ./examples/lines_in_center.png "Confounding lines inside the lane"
[image_center_regions]: ./examples/lines_in_center_regions.png "Before and after region of intrest"

---

### Reflection

### 1. Pipeline description 

My pipeline consisted of 5 steps. 
    
   1. Convert image to grayscale
   2. Apply a gaussian blur to the image
   3. Apply the Canny transformation to detect edges in the image
   4. Establish a region of interest 
   5. Apply the Hough transform to detect lines in the image
   6. Extrapolate from the lines identified in the image to draw a single line in each side of the lane.



Let's explain how the last step constructs the lines that will contain the lane. The lines identified by the Hough transform that had a slope over a certain threshold were assigned to the left and right lane markings based on the sign of the slope: lines with negative slope would be assigned to the left lane, and lines with positive slope to the right lane (the y-axis in inverted in relation to a regular axis of coordinates, so what visually appear to be positive slopes are negative, and viceversa).

The slope and bias of each line was calculated, and the final lane markings were constructed as lines with the weighted average slope an bias of the candidate lines, using the length of the lines as weights.

**Challenge video**
This approach worked fine for the first two sample videos, but for the "challenge" video, adjustments were required. The first adjustment was technical, the video had four color channels, so a parameter was added to functions generating the blank lines and lanes images to accomodate for images with different number of color channels. 
Additionally, some frames of this video contained elements inside the lane that could be confused with lane markings, moving the resulting lane towards the center of the road: 
![alt text][image_center]
To address this issue, the region of interest was redefined from a trapezoid to two bars around the expected position of the markings.
![alt text][image_center_regions]




### 2. Identify potential shortcomings with your current pipeline


This approach seems to generate reliable lane markings in videos with good light, and strong lane marks. However, the left lane marking of the 'challenge' video dissapears at some points, as the contrast is too low. The lines are also quite jittery. 

The pipeline is also untested with different lane marking colors and shapes (such as the zig-zag marks used in the UK for non-stopping areas), and with low lane visibility (such as when closely following another car). 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be  to process more than one frame at a time, so lower contrast lines could be identified when we know where to look for them, and the position of the lane markings is not allowed to change too much from a frame to the next avoiding jitter.

---

This project was completed as part the Udacity Self Driving Car Nanodegree 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)