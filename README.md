## Advanced Lane Finding Project
A software pipeline to identify the lane boundaries in a video from a front-facing camera on a car

The steps of this project are the following:
* Compute the camera calibration matrix and distortion coefficients given a set of
chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane
curvature and vehicle position.


[//]: # (Image References)

[imageorig]: ./original/test5.jpg
[imageundistort]: ./undistorted/4.jpg
[imagethreshold]: ./thresholded/4.jpg
[imagetransform]: ./transformed/4.jpg
[imagetracked]: ./tracked/4.jpg
[imagecurvefit]: ./curvefit/4.jpg
[imagemapped]: ./road/4.jpg

---

### Camera Calibration
I start by preparing "object points", which will be the (x, y, z) coordinates of the
chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x,
y) plane at z=0, such that the object points are the same for each calibration image.
Thus, objp is just a replicated array of coordinates, and objpoints will be appended with
a copy of it every time I successfully detect all chessboard corners in a test image.
imgpoints will be appended with the (x, y) pixel position of each of the corners in the
image plane with each successful chessboard detection.

I then used the output objpoints and imgpoints to compute the camera calibration and
distortion coefficients using the cv2.calibrateCamera() function. I applied this distortion
correction to the test image using the cv2.undistort() function and obtained this result:

| Original  | Undistorted |
| ------------- | ------------- |
| ![alt text][imageorig]   | ![alt text][imageundistort]  |
 
### Thresholding

I used a combination of color and gradient thresholds to generate a binary image
(thresholding code cell 6 and process(image function) in the notebook). I tried
magnitude and direction thresholding but did not necessarily get better results (at times
worse) so I removed it. For color threshold I use both HSL (S component) and HSV (V
component) and then combine their result. Here's an example of my output for this step:

| Undistorted  | Thresholded |
| ------------- | ------------- |
| ![alt text][imageundistort]   | ![alt text][imagethreshold]  |

### Perpective Transformation
The code for my perspective transform includes a function called warper() , which is in
the code cell 7 of the IPython notebook. The warper() function takes as inputs an image
( image ) and calculates the src and dest points based on a region of interest defined in
the function as offset from image edges. I define the region for the source and
destination points using a trapezoidal region in the center of the image.

Here is an example of the transformed binary:

| Thresholded  | Transformed |
| ------------- | ------------- |
| ![alt text][imagethreshold]   | ![alt text][imagetransform]  |

### Fit to Polynomial

Then I did a convolution to find window centroids (code cell 8 and #4 and #5 in cell 9)
where pixels were found. In this approach we slide a window template across the image
from left to right and any overlapping values are summed together, creating the
convolved signal. The peak of the convolved signal is where there was the highest
overlap of pixels and the most likely position for the lane marker. Then I fit my lane lines
with a 2nd order polynomial with the result (in code cell 9):

| Transformed  | Tracked | Curve Fitted |
| ------------- | ------------- | ------------- |
| ![alt text][imagetransform]   | ![alt text][imagetracked]  | ![alt text][imagecurvefit]  |

### Radius of Curvature

R curve =( (1+(2Ay+B) 2 ) 3/2 )/ |2A|

I found the curvature for the left lane and used that. Another approach could have been
find the curvatures for left and right and then average them or to find the curvature of
the mid line.


| Curve Fitted  | Mapped to road |
| ------------- | ------------- |
| ![alt text][imagecurvefit]   | ![alt text][imagemapped]  |


### Final Result

[Final video result](./output1.mp4)

### Discussion

This pipeline works well for the video provided in the project but it does not do well for the
challenge video. The improvements would likely be in the thresholding part where more
techniques and experimentation with thresholding values could result in a cleaner binary image
which forms the basis for all computation after that.
I also tried using the sliding window approach and due implementation difficulties I then followed
the convolutions approach for finding the left and right lane in the transformed image.
