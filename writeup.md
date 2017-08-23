# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Original"
[image2]: ./test_images_output/solidWhiteCurve_gray.jpg "Grayscale"
[image3]: ./test_images_output/solidWhiteCurve_blur_gray.jpg "Gaussian Smoothing"
[image4]: ./test_images_output/solidWhiteCurve_edges.jpg "Edges"
[image5]: ./test_images_output/solidWhiteCurve_edges_masked.jpg "Masked Edges"
[image6]: ./test_images_output/solidWhiteCurve_lines.jpg "Hough Transform"
[image7]: ./test_images_output/solidWhiteCurve_lines_full.jpg "Full Line"
[image8]: ./test_images_output/solidWhiteCurve_with_lines.jpg "Final"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 steps. 

![alt text][image1]

First, I converted the images to grayscale.

![alt text][image2]

Then I apply Gaussian Smoothing of kernel size 5.

![alt text][image3]

Then I apply a Canny edge detector with low threshold 20 and high threshold 120.

![alt text][image4]

Then I apply a trapezoidal mask on the detected edges, with the polygon defined by the (x,y) points: (100, bottom of image), (430, 330), (510, 330), (right of image, bottom of image).

![alt text][image5]

Then I apply a Hough Transform with parameters: rho = 1, theta = np.pi/180, threshold = 3, min_line_length = 35, max_line_gap = 25.

![alt text][image6]

Then I draw on a black image the full lines based on lines detected by the Hough Transform.

![alt text][image7]

Then I superimpose the original image with the image containing the full lines.
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by computing the slope of each line and making 2 lists: one with positive slopes, corresponding to the right line, and one with negative slopes, corresponding to the left line. In this iteration, I also keep track of the smallest y coordinate encountered, in order to reuse it as the top extremity of both lines.
For each line in each lists, I compute its length and slope, then compute the mean slope and slope standard deviation. I remove from the list the lines with slope further from the mean by more than 1 standard deviation, in order to keep lines with similar slopes and remove outliers. I keep the line with the closest slope to the mean if this condition is too stringent for the list of lines detected.
Finally, I compute the average line from the remaining lines, weighted by their lengths, in order to give more weight to the longer lines. I compute the average line slope and intersection with y=0 line, which are used to compute the x coordinates of the full line extremities, from the bottom of the image (y=bottom of image) to the smallest y previously stored.

![alt text][image8]

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming is the inability to handle curves. The resulting full line is not representative of the lane.

One other shortcoming is the vulnerability to small lines detected from road marks which confuse the average full line.

### 3. Suggest possible improvements to your pipeline

A possible improvement to handle curves is to reduce the scope of the edges mask, focusing on the near foreground of the road and excluding the main curve.

Another potential improvement could be to refine further the Hough Transform parameters to reduce the amount of small lines detected, improvnig the rejection of lines detected from unrelevant road marks.
