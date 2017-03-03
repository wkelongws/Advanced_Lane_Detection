##Advance Lane Detection

###In this project I detect lane through the center camera image in car using pure computer vision techniques 
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

[distortedchessboards]: ./output_images/distortedchessboards.png "distortedchessboards"
[distortedimages]: ./output_images/distortedimages.png "distortedimages"
[warpedtestimages]: ./output_images/warpedtestimages.png "warpedtestimages"
[chessboardcorners]: ./output_images/chessboardcorners.png "chessboardcorners"
[straightlines2]: ./output_images/straightlines2.png "straightlines2"
[screen_output]: ./output_images/screen_output.png "screen_output"
[pavementfilter_1]: ./output_images/pavementfilter_1.png "pavementfilter_1"
[pavementfilter_2]: ./output_images/pavementfilter_2.png "pavementfilter_2"
[straight_lane_for_real_distance]: ./output_images/straight_lane_for_real_distance.png "straight_lane_for_real_distance"
[test_images_lane_found]: ./output_images/test_images_lane_found.png "test_images_lane_found"
[pavementfilter_3]: ./output_images/pavementfilter_3.png "pavementfilter_3"
[pavementfilter_4]: ./output_images/pavementfilter_4.png "pavementfilter_4"
[straightlines2_binary]: ./output_images/straightlines2_binary.png "straightlines2_binary"



## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

###All related codes can be found in "Advaned_Lane_Detection.ipynb". The correct directions to the codes can be found under each section below

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` are the corners points detected from the provided chessboard images. The detected points are shown below:

![alt text][chessboardcorners]

Then the output `objpoints` and `imgpoints` are used to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. I applied this distortion correction to the chessboard images using the `cv2.undistort()` function and obtained this result: 

![alt text][distortedchessboards]

To find the corresponding codes for the described tasks above, you can search: 

* "read in the provided chessboards. output image points and object points"

* "perform the chessboard function. get object points and image points"

* "interactively navigate all chessboard images"

* "output the visualization all chessbard images with corners drawn"

* "calibriate camera using objpoints and imgpoints. output transision matrix"

* "visualize all distorted chessboards "

in "Advaned_Lane_Detection.ipynb".

###Pipeline

### the frame-wise process includes: distortion, thresholding, warping and lane finding
### Then video-wise process requires Lane() class the keep tracking the detected lane from each frame

####1. Distortion

I applied the distortion coefficients calculated from chessboard images to the test images. And the distorted test images look like this:

![alt text][distortedimages]

To find the corresponding codes for the described tasks above, you can search: 

* "define image distortion function"

* "visualize all distorted test images"

in "Advaned_Lane_Detection.ipynb".

####2. Thresholding

I used a combinations of different thresholds. Besides the gradient filter, gradient_magnitude filter, grandient_direction filter and color filter, I also use a pavement filter. The pavement filter are defined to clip out sample patches of road surface, calculate the histogram of the sample patches and filter the raw image histograms using the pavement histogram. The goal is to adaptively filter out the pavement. The effect of the pavement filter will be shown using 4 examples below:

* example 1:

The road in this image has constant pavement color. The filter can efficiently filter out the pavement.

![alt text][pavementfilter_3]

* example 2:

The road in this image has constant pavement color but it is a brighter one different from example 1. The filter can efficiently filter out the pavement adptively.

![alt text][pavementfilter_1]

* example 3:

The road in this image has inconsistant  pavement color. The sample patches covered both the colors so the filter can efficiently filter out the pavement adptively.

![alt text][pavementfilter_2]

* example 4:

The road in this image has inconsistant  pavement color. The sample patches did not cover both the colors so the filter only filtered out the pavement with the same color as in the sampled patches. This illustrated that the pavement filter can not be relied on solely but it can be a good supliments for the other filters.

![alt text][pavementfilter_4]

I have a program to interactively play with the combined thresholding function and fine tune the hyper parameters, since markdown does not support the interactive window, you can find it in the codes.

To find the corresponding codes for the described tasks above, you can search: 

* "define binary thresholding filter based on pavement color"

* "visualize pavement filter thresholding method for generate binary images"

* "define a comprehensive thresholding function which combined grad filter, color filter, mag filter and pavement filter."

* "interactively play around with the combined binary filter"

in "Advaned_Lane_Detection.ipynb".

####3. Warping

For warping, I examined the two provided test images with straight lanes. I manually find found the lane frame of the original image and cast them to a rectangular box. This example shows the effect after applying the warping.

![alt text][straightlines2]

Then combine the thresholding with warping we will have the binary warped image of the straight line image like this:

![alt text][straightlines2_binary]

This image shows the effect after apply the thresholding and warping the all the test images:

![alt text][warpedtestimages]

To find the corresponding codes for the described tasks above, you can search: 

* "define basic functions for image warping"

* "interactively play around with image warping and filtering"

* "visualize combined effect of warping and thresholding on all test images"

in "Advaned_Lane_Detection.ipynb".

####4. Lane finding

I used the codes provided by Udacity in the class to form two functions: one performs line search by sliding window and the other performs line search using existing fitted poly line.

This example shows the two different methods side by side, on the left is the sliding window method and on the right is the existing poly line method:

[alt text][straight_lane_for_real_distance]

I also calculate the ratio of pixel to real distance from the above image. 

I used these value to calculate the ratio:

* 1 dash line = 10 ft
* 1 gap between dash lines = 30 ft 
* lane width = 12 ft

From the image above, 3 dash lines and 2.5 gaps span 720 pixels in vertical direction, and 1 lane width spans (776-555) pixels in horizontal direction

Then I calculated the atio of pixel to real distance as:

* y_ft_per_pix = 105/720 = 0.146 ft/pxi # fts per pixel in y dimension
* x_ft_per_pix = 12 / (776-555) = 0.054 ft/pxi  # fts per pixel in x dimension

A curvature calculation function takes in these ratios and the fitted poly line and returns the radius of the curvature.

Up to here we have finished the frame-wise process. This image below recap the entire process:

[alt text][test_images_lane_found]

The first row shows the raw test images. The second row shows the warped binary image after applying thresholding and warping on the raw test images. I manually draw an "attention area" whose bounaries are shown by the dashed white lines. Everything outside the "attention area" will be erased out as shown in the third row. The forth row shows the histogram from the third row. The histograms are used to find the starting point of the lane. The last two rows show the detected lanes using sliding window and existing poly respectively. The calcualted curvature for the detected lane are marked on top of each image.

To find the corresponding codes for the described tasks above, you can search: 

* "define all functions and classes for lane searching on warped binary images"

* "visualize line searching on warped binary images from provided test images with straight lanes."

* "define a function to calculate curvature from the fitted poly line"

* "visualize the entire frame-wise process on test images."

in "Advaned_Lane_Detection.ipynb".

####Finally video-wise process

For processing the project video, I carefully defined the Lane() class. Lane() will store detected lanes from up to 6 previous frames. A weighted average fit, giving higher weights to newer frames, will be used as the detection for the current frame. In order to easier find out what is going wrong in the lane finding process, I segmented the output screen to show the final detection as well as all the intermediate results.

This image demonstrates how the output video looks:

[alt text][screen_output]

To find the corresponding codes for the described tasks above, you can search: 

* "define three main classes: Line(), RoadLine() and Lane() to handle video-wise processing"

* "interactively play around with the Lane() class to check the snapshots from the final output video"

* "define a function to handle video frame by frame and output result to a new video"

in "Advaned_Lane_Detection.ipynb".


###Final project video

Here's a [link to my video result](https://youtu.be/wXoWPThs0CQ)

---

###Discussion

This project took me a fairly long time to finish. I have applied the current pipeline on the challange videos but it doesn't work well. Thanks to the output screen I designed I can clearly see which part went wrong.

I have found the thresholding step is the most most critial step in the entire computer vision analysis. The quality of all the outputs highly depend on the quality of the thresholding. Shading, different pavement color, sunlight, vague lane marking and all other natual things can significantly affect the thresholding. I believe if I want to perfect the thresholding I could not rely on  just one filter or just one combination of different filters. The ultimate way would be go searhcing through the entire space of all possible combinations of all possible filters until we find a acceptable binary solution. Maybe even that is not enough, we need to also patch the image and filter it patch by patch. But this cannot be the best practial solution because it is very computing intensive and cannot be applied in real-time application. I need to find a better way in the future study. Other important hyper parameters include the box size or search margin in the line searching functions. Smaller margin will provide more accurate result but are also less robust. These parameter also affected by the thresholding. If the thresholding is perfect then it would be very easy to choose the following hyper parameters. After all, thresholding is the bottleneck.  

