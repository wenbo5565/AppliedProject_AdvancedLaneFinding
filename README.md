### Applied Project - Traffic Lane Detection

#### Overview

Traffic lane detection is an essential function for a self-driving car system. In this project we build a software pipeline to detect traffic lanes given a camera placed in front of a car. We test our pipeline on a recorded video clip from the camera. Our pipeline works very well on the clip.

Here is a sub clip of the ouput video for demo purpose

  <img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/output_video.gif"  height="50%" width="50%">

The [complete video](https://www.youtube.com/watch?v=_-b3N_NYUBg) can be find at YouTube

#### Technical Summary

We organize our pipeline into the following steps.

* Camera calibration and image distortion correction
* Color and gradient thresholding
* Perspective transformation ("birds-eye view").
* Lane pixel detection and curve fitting
* Draw detected lanes onto original frames

Now we briefly demonstrate the goal and effect of each step above. Technical details can be found at the link to Ipython Notebook in the end.

#### Camera calibration and image distortion correction

When a camera transforms a 3D physical world to a 2D image, this transformation isn't perfect. Distortion changes what the shape and size of these 3D objects appear to be. So, the first step in analyzing camera images, is to undo this distortion so that we can get correct and useful information out of them.

<!-- attach distortion image -->

The idea to calibrate a camera and undistor images is as follows:
  * Find corners of a chessboard in 3D physical world. Save their coordinates in an array
  * Take photos of the chessboards from different perspectives. Find the corners' coordinates in these images and save them in another array
  * Put these two arrays into opencv function to compute distorted coefficients
  * Appply distorted coefficients to undistort any image taken by the camera.
  
The following is the original image from a camera and its undistorted version

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/undistortion.png" height=75% width=75%>

#### Color and gradient thresholding

Color and gradient thresholding is an important step in lane detection. The idea is to separate traffic lanes from other objects in using their quantitative characteristics in color space or gradient space. The color space could be RGB or HLS. The gradient space could be gradient on X direction, Y direction or mixed.

The following is traffic lanes in an original image and post-threholding color/gradient space

HLS(S Channel) + Sobel X

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/hls_xsobel.png" height="75%" width="75%">

#### Perspective transformation ("birds-eye view")

Now we need to pick out these two lanes. Since the camera position is fixed relative to the car, the lanes will only appear in a approximately fixed area. Therefore we can select an area containing only the lanes. In addition, as the camera is not parallel to the road plane, lanes are not parallel in the original image. Therefore we must transform the selected area from the original perspective to an birds-eye perspective.

The idea to achieve this transformation is similar to camera calibration. We need to choose coordiantes for a fixed object in both the original image space and the "birds-eye" view space. Then we leverage opencv function to calculate a transformation matrix. We apply this matrix to transform images.

The following is traffic lanes in an original space and birds-eye space.

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/unwarped.png" height="75%" width="75%">

#### Lane pixel detection and curve fitting

Now we have picked up lanes. In this step we apply sliding-window search approach to detect pixels belonging to the respective left and right lanes. Then we fit polynoimal regression to obtain our detected lanes.

The following images show sliding-window search anf curve fitting.

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/slide_window_search.png" height="50%" width="45%">

#### Draw detected lanes onto original frames

The last step is drawing the detected lanes back to the original image.

<img src="https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/images/result.png" height="80%" width="80%">

#### Technical Details

Technical details and python code can be found at [Code and Implement Details](https://github.com/wenbo5565/AppliedProject_AdvancedLaneFinding/blob/master/Advanced%20Lane%20Findings.ipynb)



 
