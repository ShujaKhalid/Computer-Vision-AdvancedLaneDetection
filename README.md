# Advanced Lane Finding Project
The goals / steps of this project are the following:
- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
- Apply a distortion correction to raw images.
- Use color transforms, gradients, etc., to create a thresholded binary image.
- Apply a perspective transform to rectify binary image ("birds-eye view").
- Detect lane pixels and fit to find the lane boundary.
- Determine the curvature of the lane and vehicle position with respect to center.
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle
position.

[//]: # (Image References)

[image1]: ./imgs/chessboard_image_1.png "chess1"
[image2]: ./imgs/chessboard_image_2.png "chess2"
[image3]: ./imgs/chessboard_image_3.png "chess3"
[image4]: ./imgs/distortion_correction.png "Visualization"
[image5]: ./imgs/r_layer.png "Visualization"
[image6]: ./imgs/sobel_layer.png "Visualization"
[image7]: ./imgs/stacked_layer.png "Visualization"
[image8]: ./imgs/birds_eye_view.png "Visualization"
[image9]: ./imgs/polylines.png "Visualization"
[image10]: ./imgs/final_image.png "Visualization"
[image11]: ./imgs/final_video.gif "Visualization"
[image12]: ./imgs/final_challenge.gif "Visualization"


## Approach

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the
world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are
the same for each calibration image. Thus, objp is just a replicated array of coordinates, and objpoints
will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.
imgpoints will be appended with the (x, y) pixel position of each of the corners in the image plane with
each successful chessboard detection. The results of the detection are illustrated in the figure below:

![image1] ![image2] ![image3]

## Camera Calibration

I then used the output objpoints and imgpoints to compute the camera calibration and distortion
coefficients using the cv2.calibrateCamera() function. I applied this distortion correction to the test
image using the cv2.undistort() function and obtained this result:

![image4]

In the figure above, the calibration matrix is applied to each of the images to remove distortion.

## Pipeline (single images)

The code for my perspective transform is includes a function called warper() , which appears in lines 1
through 8 in the file example.py (output_images/examples/example.py) (or, for example, in the 3rd code
cell of the IPython notebook). The warper() function takes as inputs an image ( img ), as well as source
( src ) and destination ( dst ) points. 

The resulting binary image has been created by stacking a Sobel gradient layer and an R (red layer) layer from the original RGB image. The R layer provides an acceptable binary mask that demarks the portion of the lane that is closer to the camera. Whereas the Sobel gradient layer is produced by taking the gradient of the image in both x and y directions and using the absoute value of the two. The layer provides some detailed information of the lane that is lost if only the individual colour layers are used.  

The following image is a stack of the two layers. The R layer mask is denoted in blue whereas the Sobel gradient layer is denoted in green.  

![image5]

![image6]

![image7]

This step was followed by creating a perspective transformation to adequately identify the previously detected lines on the road. I verified that my perspective transform was working as expected by drawing the src and dst points onto
a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![image8]

I then attempted to used a filtered histogram of a set of rows along the image to identify the peak locations which correspond to the existence of a line. A small bounding box is drawn around the identified areas along the length of the lane. Within the boxes, the points that are most likely to represent a lane are used to draw a 2nd order polynomial.

![image9]

Based on the results above, the method does a great job of identifying the lanes. The radius of curvature is then calculated using this information.

The pipeline can now be applied to a video along with the radius of curvature. 

![image10]

![image11]

![image12]

## Lessons Learned
The entire process discussed above dwarfs the earlier introductory lane detection project. Understanding the contents of the image can help in extracting more information than what is available on the surface. The red layer of the images did a great job of capturing the parts of the lane that were closer to the camera whereas the Sobel gradient layer captured the features of the lanes that were further away from the camera. Once this information is made available, the mind-blowing concept of perception transformation allows us to calculate the radius of curvature of the lanes very effectively by converting the real world coordinates to 2D coordinates. Combining all of these tools at our disposal with simple mathematical tools can allow for accurate and robust lane detection. 
