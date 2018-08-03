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

Now we demonstrate the goal and effect of each step above

##### Camera calibration and image distortion correction

When a camera transforms a 3D physical world to a 2D image, this transformation isn't perfect. Distortion changes what the shape and size of these 3D objects appear to be. So, the first step in analyzing camera images, is to undo this distortion so that we can get correct and useful information out of them.

<!-- attach distortion image -->

The idea to calibrate a camera and undistor images is as follows:
  * Find corners of a chessboard in 3D physical world. Save their coordinates in an array
  * Take photos of the chessboards from different perspectives. Find the corners' coordinates in these images and save them in another array
  * Put these two arrays into opencv function to compute distorted coefficients
  * Appply distorted coefficients to undistort any image taken by the camera.
  
The following is the original image from a camera and its undistorted version


##### Color and gradient thresholding

##### Perspective transformation ("birds-eye view")

##### Lane pixel detection and curve fitting

##### Draw detected lanes onto original frames

#### Technical Details

 
