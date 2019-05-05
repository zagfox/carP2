## Writeup for P2

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
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

All code is in p2 python notebook.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

I read in all chessboard photos with glob.glob('camera_cal/calibration*.jpg'). Try to find 9,6 cross point with `cv2.findChessboardCorners`, if not found, the image is skipped. The objective points is generated manually. After
scanning all images, I calibrate the camera with `cv2.calibrateCamera`.

Data is stored for later part of project. A sample calibrate can be found in `output_images/undistort.jpg`.


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I use `cv2.undistort(img, mtx, dist, None, mtx)` to undistort the image.

A sample calibrate can be found in `output_images/undistort.jpg`.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of x dimension saturation and light information to create binary image. The threshold is selected by hand.
A sample result of those 2 is shown in `output_images/color_binary.jpg`.

The binary result is in `output_images/binary.jpg`.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

To perform perspective transform, I manually selected points in image as the source points. And destination point is selected as a rectangular. function `cv2.getPerspectiveTransform(src, dst)` is used to get the transform metric.

Then I use `cv2.warpPerspective` to transform the binary image. Result can be found in `output_images/binary_warped.jpg`.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I use sliding window to find target lines. First I calculate histogram of the binary image in the bottom half of the image with
`np.sum(binary_warped[h//2:,:], axis=0)`. Then I use a 9 layer sliding window, to find each layer's left and right position of the lane. The result can be found in `output_images/window_img.jpg`.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I use code
`left_curverad = ((1 + (2*left_fit[0]*y_eval + left_fit[1])**2)**1.5) / np.absolute(2*left_fit[0])
 right_curverad = ((1 + (2*right_fit[0]*y_eval + right_fit[1])**2)**1.5) / np.absolute(2*right_fit[0])
 print(left_curverad, right_curverad)`
to calculate the curverature of lanes.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

A sample output image can be found in `output_images/result.jpg`.

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Video can be found in `output_images/project_video_output.mp4`.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The technique I used comes from tutorial. There are places that can be improved.

1. Binary image finding threshold can be adjusted with more sample image. I select them by hand.
2. Binary image can use more combined mechanism like ratio of x and y gradient.
3. Sliding window may fail if the road is curved and the center cannot separate left and right.
4. In sliding window, some point that far a way can be treated as nonise.
5. Left and right lane should be parallel, this information can be used in lane finding.
