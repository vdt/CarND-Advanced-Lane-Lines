# Advanced Lane Finding Project

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/orig-undistort.png "Undistorted"
[image1a]: ./output_images/undistorted.png "Undistorted"
[image1b]: ./output_images/distorted.png "Distorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3a1]: ./output_images/thresholded1.png "Binary Example"
[image3a]: ./output_images/thresholded-gradient-direction.png "Binary Example"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[image6a]: ./output_images/video-screenshot.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric Points](https://review.udacity.com/#!/rubrics/571/view) 

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](README.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in `1. Camera Calibration` and `2. Distortion Correction` Section of the Jupyter notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image1a]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this step is contained in `3. Color and Gradient Threshold` Section of the IPython notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb).

I used a combination of color and gradient thresholds to generate a binary image . Here's an example of my output for this step. 

![alt text][image3a1]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for this step is contained in `4. Warp with Perspective Transform` Section of the IPython notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb) . The code for the perspective transform function `perspective_transform()` includes a function called `drawQuad()`. The `get_perspective_rectangles()` in `7. Calculate Curvature` Section of the IPython notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb), takes as inputs an image (`img`), and returns (`src`) and destination (`dst`) points.  The output of the same is given in `Out[15]:` of the IPython notebook.
I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3a]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

We first check the histogram of the lower of image and find the two peaks for the left and right lines. Then we use the sliding window method to work our way upwards and find the relevant points in the image which mark the lane. Next, we use the `np.polyfit()` method to fit a second degree polynomial to these points in the `fit_polynomials()` function in `6. Sliding Window and Fit Polynomial` of the Jupyter Notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb).



#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Given the detected points, we determine a second degree polynomial that fits these points, and then we calculate the radius of curvature of this polynomial. Also, we convert from the pixel space to real space in meters. I did this in `get_curvature()` function in `7. Calculate Curvature` of the Jupyter Notebbok [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb) .

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `8. Process Image` in the Jupyter Notebook [Advanced-Lane-Lines.ipynb](Advanced-Lane-Lines.ipynb) in the function `process_image1()`.  Here is an example of my result on a test image:

![alt text][image6a]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

It is a Challenge to select optimum parameters for the gradient thresholds because we want to do it in a way that works for a wide number of scenarios. This was achieved by testing it on the 6 test images that are given.

The pipeline fails on  test images, mostly because we don't keep state of previous video frames as of now. We can make the processing more efficient by making it remember the last detected lane line and then using that as a hint for detecting the lane in the next video frame.

To make the pipeline more robust, we should add checks like checking that the radius of curvature is similar for both the left and right lane lines. Also, it should continue to work if just a single lane line is visible.  
