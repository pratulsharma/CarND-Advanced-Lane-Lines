## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./examples/Road_image_undistorted.png "Road Transformed"
[image3]: ./examples/binary_combo_example.png "Binary Example"
[image4]: ./examples/warped_straight_lines.png "Warp Example"
[image5]: ./examples/color_fit_lines.png "Fit Visual"
[image6]: ./examples/example_output.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration and Distortion correction

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
The code for this step is contained in the Cell 1 (cameraCalibration function).
The output of the cameraCalibration function is 
 - Object points : 3d  points in real world space 
 - Image points : 2d points in the image plane

Steps
- Twenty one different images of chessboard pattern are used for camera calibration. 
- convert the images from BGR 2 GRAY scale
- Find the chess board corner using cv2.findChessboardCorners function
- Use cv2.calibrateCamera function to do camera calibration. The output of which is used to undistort the image

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Following image shows an example of distortion correction on the image of the road.
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (Look at thresholding(img) function in the CarND-Advanced-Lane-Finding.ipynb). 
Image is first converted to HLS color space and then Sobel X transform is applied. In the given image, HLS color space works the best in various different lighting conditions.
Here's an example of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`.  I chose the hardcode the source and destination points in the following manner:

```python
# Coordinates of the source points 
    sbot_l = (300,720) 
    sbot_r = (1130, 720)
    stop_l = (592, 455)
    stop_r = (690, 455)

# Coordinates of the destination points 
    dbot_l = (320,720) 
    dbot_r = (1100,720)
    dtop_l = (320 ,1)
    dtop_r = (1130,1)
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 300, 720      | 320, 720        | 
| 1130, 720      | 1100, 720      |
| 592, 455     | 320, 1      |
| 690, 455      | 1130, 1        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

A sliding window search is performed to detect the lane pixels (function `sliding_window`). A histogram is used as a starting point to identify the starting points of the lane. Once the starting points are initialized, sliding window search is used to detect the remaining lane pixels.
Number of windows used = 15
Number of minimum pixels detected = 80
Margin (width of the window +/- margin) = 80
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature is calculated using the formula from the class material. The function `radius` contains the code for radius calculation.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The function `draw_lanes` perform this task.

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

- I used only the mean between the 20 frames to smoothen the lanes on the video. I have not used any other technique to reject the incorrect lanes due to the time pressure. The project can further be improved by rejecting the frames where the lanes are detected much further away from where they should be. Techchniques like - average distance between the lanes, left and right radius values can be used between the subsequent frames to further improve the output. 
- Other color transform can be studied to see if the output can be improved.
