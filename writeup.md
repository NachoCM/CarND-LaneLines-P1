# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image_low_slope]: ./examples/low_slope_example.jpg "Low slope lines"
[image_center]: ./examples/lines_in_center.png "Confounding lines inside the lane"
[image_center_region]: ./examples/lines_in_center_region.png "Before and after region of intrest"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
    1. Convert image to grayscale
    2. Apply a gaussian blur to the image
    3. Apply the Canny transformation to detect edges in the image
    4. Establish a region of interest 
    5. Apply the Hough transform to detect lines in the image
    6. Extrapolate from the lines identified in the image to draw a single line in each side of the lane.

In order to filter out the lines that are not part of lane markings, I included an additional parameter "min_slope", to keep only lines that are not too horizontal. This would avoid situations like this: 
![alt text][image_low_slope]

A new function "draw_lanes" was created based on "draw_lines". It draws just two lines, one at each side of the lane. The lines identified by the Hough transform that had a slope over the threshold, were assigned to the left and right lane markings based on the sign of the slope: lines with negative slope would be assigned to the left lane, and lines with positive slope to the right lane (the y-axis in inverted in relation to a regular axis of coordinates, so what visually appear to be positive slopes are negative, and viceversa).

The slope and bias of each line was calculated, and the final lane markings were constructed as lines with the weighted average slope an bias of the candidate lines, using the length of the lines as weights.

**Challenge video**
This approach worked fine for the first two sample videos, but for the "challenge" video, adjustments were required. The first adjustment was technical, the video had four color channels, so a parameter was added to functions generating the blank lines and lanes images to accomodate for images with different number of color channels. 
Additionally, some frames of this video contained elements inside the lane that could be confused with lane markings: 
![alt text][image_center]
To address this issue, the region of interest was redefined from a trapezoid to two bars around the expected position of the markings.
![alt text][image_center_region]




### 2. Identify potential shortcomings with your current pipeline


This approach seems to generate reliable lane markings in videos with good light, and strong lane marks. However, the left lane marking of the 'challenge' video dissapears at some points, as the contrast is too low. The lines are also quite jittery. 

The pipeline is also untested with different lane marking colors and shapes (such as the zig-zag marks used in the UK for non-stopping areas), and with low lane visibility (such as when closely following another car). 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be  to process more than one frame at a time, so lower contrast lines could be identified when we know where to look for them, and the position of the lane markings is not allowed to change too much from a frame to the next avoiding jitter.
