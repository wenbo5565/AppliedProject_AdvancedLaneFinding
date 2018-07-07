### Advanced Lane Finding Project

**Author: Wenbo Ma**

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
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the cell In [3] of the [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  

`imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/undistortion.png">

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/undistortion.png">

The code is in cell [4] of [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)

Steps:

* call cv2 function cv2.calibrateCamera with objpoints and imgpoints from the camera calibration step in order to obtain matrix and distortion coefficient
* apply the matrix and distortion coefficient into cv2 function cv2.undistort to correct the image




#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image 

Steps:
* apply thresholding (100,255) on S channel of the image
* apply thresholding (20,255) on the absoluate gradient of x direction using cv2.sobel function
* combine the above two steps: set pixel to 1 (white) if s channel or x gradient is 1

The code is in cell [5-7] of [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)


Here's an example of my output for this step. 

**S Channel Thresholding**

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/s_channel.png" height="80%" width="80%">

**Sobel X Thresholding**

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/x_sobel.png" height="80%" width="80%">

**HLS(S Channel) + Sobel X**

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/hls_xsobel.png" height="80%" width="80%">



#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `get_transform_matrix()` and `persepctive_transform()`, which appears in cell [9] of [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)

The `perspective_transform` function takes as inputs an image, as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([(578,461),
                  (200,717), 
                  (1109,717), 
                  (700,461)])
dst = np.float32([(200,0),
                  (200,717),
                  (1109,717),
                 (1109,0)])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 578, 461      | 200, 0        | 
| 200, 717      | 200, 717      |
| 1109, 717     | 1109, 717      |
| 700, 461      | 1109, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/unwarped.png" height="80%" width="80%">

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I apply the "sliding window search" in cell [12]  [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)

The steps are summarized as:
* use histogram method to get the base (starting) x pixel for each lane (y=720)
* use a slide-window-search in bottom-up fashion to detect all pixels for each lane
* fit a polynomial regression for detected pixels in each lane (x pixel is dependent variable and y pixel is the independent pixel)

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/slide_window_search.png" height="50%" width="50%">

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cell [14]  [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)

Steps for radius (for each of lane)
* Get the fitted x and y in the pixel space and convert them to physical space
* Fit the polynomial in the physical space
* Use the coefficients in the polynomial to compute the radius for each lane
* Average the radius

Steps for car position offset
* Use the average of frame width as the car center
* Use the average of detected lanes (y=720) as the lane center
* Compute the difference between (1) and (2) as the offset. If lane center is larger than car center, then the car is offset to the left and vice versa.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in cell [15]  [Jupyter Project Notebook](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)  Here is an example of my result on a test image:

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/result.png" height="80%" width="80%">

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result in mp4 format](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/project_output_video.mp4)

Here is the cropped gif version of my result video:

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/output_video.gif" >



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

* Initially I just use s-channel thresholidng to get both lanes in a binary image. But s-channel thresholding doesn't work well to detect the dotted lane when there is change in pavement on the ground. For example, in the frame attached below, the s-thresholding will miss the right lane. To solve the problem, I add a sobel gradient thresholding on the x coordinate which turned out to be effective to detect the dotted line.

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/difficult1.png" height="50%" width="50%>

* The current thresholding pipeline includes s-channel thresholidng (HLS) and Sobel X thresholding. S-channel thresholding works very well in the project video as one of the lanes is solid lane. If the car drives in the middle lane (both left and right lanes are dotted lanes), it may not work well. 
