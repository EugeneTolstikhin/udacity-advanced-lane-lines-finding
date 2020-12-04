## Writeup

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

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this point is a function `camera_calibration()` inside the [Jupyter notebook](./advanced_lane_finding.ipynb)

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. The calibration coefficients and new camera matrix are kept [here](./output_images/cam_calib.p).

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The undistortion process is written inside `undistort_image(img)` [here](./advanced_lane_finding.ipynb). See **undistorted** image inside [pdf](./output_images/plots_test1.pdf)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (see function `binary_image(img)` [here](./advanced_lane_finding.ipynb)). The result of image tranformation is shown as **binary** image inside [pdf](./output_images/plots_test1.pdf)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper(img)` [here](./advanced_lane_finding.ipynb). The source (`src`) and destination (`dst`) points are kept statically inside the function. I chose the hardcode data for transformation:

```python
src = np.float32([  [200, 720],
                    [1100, 720],
                    [595, 450],
                    [685, 450]])

dst = np.float32([  [300, 720],
                    [980, 720],
                    [300, 0],
                    [980, 0]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. See **Bird eye view** image inside [pdf](./output_images/plots_test1.pdf)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I tried to find lane line pixels. The process is written inside `find_lane_pixels(warped_binary_img)` [here](./advanced_lane_finding.ipynb)

See **Poly** image inside [pdf](./output_images/plots_test1.pdf)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The procedure is written inside function `measure_curvation_real()` [here](./advanced_lane_finding.ipynb)

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the funcction `visualize()` [here](./advanced_lane_finding.ipynb). Result is a **Result** image inside [pdf](./output_images/plots_test1.pdf)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_videos/project_video_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

During implementation of this project I faced the problem that the approach has some flaws, especially during frequent environment brightness changes. The result can be seen in all the videos when the green area jumps sometimes when the brightness and lanes' shapes change frequently. As the points for improvements/taking under control I chose the following functions:

* the procedure of binary image calculation (looks like some extra filters, masks, etc. need to be considered to make the algorithm works more precisely).
* the curvation calculation (the math there need to be double checked)
* keeping and extracting the lanes from the previous frames during the video processing (I can assume that this algorithm has the points for improvements)
