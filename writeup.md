# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline to detect lane lines uses the concept of vanishing points to determine the left and the right lane lines. The following assumption is important:

* Given a frame from a camera that is roughly situated at the center of the car, the lane lines on either side will converge, due to perspective, to a point on the horizon. This point is called the vanishing point.

The pipeline can be decomposed into 5 major steps.

1. The first step is preprocessing the image. Here we convert the image from color to grayscale followed by guassian blur. The guassian blur has the effect of supressing the high frequency components in the image which is helpful in the following substep, which is Canny edge detection. This gives us an edge image that is passed on to the next step.

2. The second step is extracting the line segments in the edge image. We use hough transform to detect the lines. To speed up the process, we restrict the processing in a region that is most to contain the lane lines. Once we have all the line segments, we calculate the slopes of these which are used in the following steps.

3. In this step, using the slopes we calculated above, we find the best matching line segment that is most likely to represent a lane line. This process is done individually for each lane line.

4. Once we have identified the candidate lane lines, we compute the vanishing point at which these converge. The vanishing point is an imaginary point on the horizon where parallel seem to converge due to perspective transform. This assumes that the image is roughly centered between the lane lines.

5. The final step is estimating the bounds for the lines that are to be drawn on the image. This is done using the vanishing point computed above and a heuristic specifying the bottom points of both the lines. Additionally, these bounds are added to a running average of previously computed bounds to smoothen out any jitters.


### 2. Identify potential shortcomings with your current pipeline

Since the pipeline detects vanishing point for parallel lines, the pipeline will fail if it fails to detect this point. This can happen to due to variety of reasons, but a common one is the curvature of road.

One more shortcoming would be if the contrast is lacking between the lane lines and the road. This can happen in many scenarios, a few of which are
* Glare
* Rain
* Washed out markers
* etc.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use a better method to keep track of the previous frames' lane lines and then extrapolate the future frames.

Another potential improvement could be to use better features than edges. Edge features are pretty rudimentary and may not provide a robust detection result. An alternative could be HOG features.
