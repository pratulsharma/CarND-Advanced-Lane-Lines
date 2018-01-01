## Writeup Template

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
[image3a]: ./examples/region_of_interest.png "Road Transformed"
[image3]: ./examples/binary_combo_example.png "Binary Example"
[image4]: ./examples/birds_eye_view.png "Warp Example"
[image5]: ./examples/color_fit_lines.png "Fit Visual"
[image6]: ./examples/drawn_lane_lines.png "Output"
[video1]: ./project_video_cha.mp4 "Video"

### Camera Calibration and Distortion correction

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
The code for this step is contained in the `cameraCalibration()` function.
The output of the `cameraCalibration()` function is 
 - Object points : 3d  points in real world space 
 - Image points : 2d points in the image plane
Steps
- Twenty one different images of chessboard pattern are used for camera calibration. 
- First images are converted from BGR 2 GRAY scale.
- Then chess board corners are identified using `cv2.findChessboardCorners()` function.
- Then `cv2.calibrateCamera()` function is used to do camera calibration. 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Following shows an example of distortion correction on the image of the road.

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image using `thresholding3()` function.
Multiple Thresholds are applied on the image.
- Extracted the S-channel of the original image in HLS format and combined the result with the extracted V-channel of the original image in HSV format.
- Both, Sobel X and Y operators are used to filter the original image for the strongest gradients.

Here's an example of the output of this step.  

![alt text][image3]

Region of interest is extracted after thresholding.

![alt text][image3a]
#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is included in a function called `perspective_transform()`. 


This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 545, 460      | 0, 0        | 
| 735, 460      | 1280, 0      |
| 1280, 700     | 1280, 720      |
| 0, 700      | 0, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

A sliding window search is performed to detect the lane pixels (function `sliding_window`). A histogram is used as a to identify the starting points of the lane. Once the starting points are identified, sliding window search is used to detect the remaining lane pixels.
- Number of windows used = 10
- Number of minimum pixels detected = 80
- Margin (width of the window +/- margin) = 80

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature is calculated using the formula from the class notes. The function `radius` contains the code for radius calculation.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The function `draw_lanes` perform this task.

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_cha.mp4)

Here's the Youtube link 
#### **YouTube:** https://youtu.be/BWoM_VqezPA 
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

- I used only the mean of 15 frames to smoothen the lanes on the video. I have not used any other technique to reject the incorrect lanes due to the time pressure. The project can further be improved by rejecting the frames where the lanes are detected much further away. Techchniques like - average distance between the lanes, left and right radius values can be used between the subsequent frames to further improve the output. 
- Other color transform can be studied to see if the output can be improved.
