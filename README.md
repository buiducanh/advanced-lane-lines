
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

[image1]: ./output_images/calibration1.png "Undistorted"
[image2]: ./output_images/test1_undist.png "Road Transformed"
[image3]: ./output_images/test1_binary.png "Binary Example"
[image4]: ./output_images/perspective_warp.jpg "Warp Example"
[image5]: ./output_images/fit_output.jpg "Fit Visual"
[image6]: ./output_images/lane_plotted.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! Additionally, all the code lives in `./detect_lane_lines.ipynb`

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 2nd and 3rd code cell of the IPython notebook.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates (produced using mgrid for 9x6 corners), and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image in a method `binaryWarp` within code cell 9 in the notebook.  Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspectiveWarp()`, which appears in the 5th code cell of the IPython notebook.  The `perspectiveWarp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose to hardcode the source and destination points.

Here are the source and destination points:

    'src': np.float32([(565, 470), (728, 470), (1110, 700), (226, 700)]),
    'dst': np.float32([(300, 0), (860, 0), (860,700), (300, 700)])

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 565, 470      | 300, 0        | 
| 728, 470      | 860, 0        |
| 1110, 700     | 860, 700      |
| 226, 700      | 300, 70       |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial, in the 10th code cell, kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the 11th and 12th code cells.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the 13th code cell in the function `drawFitPolyFromWarp()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The biggest issue I had was getting the thresholding parameters right to extract the lane lines, especially when there is a lot of shadow. When there is a lot of shadow, the binary image won't be able to catch the lane lines. I would need to take more time to get the parameters right, and I could also look into shadow detection and change the threshold dynamically to catch the lane lines within the shadows.

Another issue is that when the road is really bad and had scratch marks, the pipeline might catch the lane lines but also catch the noises from the marks. The pipeline could benefit from an averaging step that will use n past measurements that are weighted by confidence (using different criterias like how parallel the lines are, the curvature, and the number of pixels detected).

I also hardcoded the perspective warp points, and it would benefit from a better way to use the image shapes to calculate these points.

The pipeline also basically outputs nothing when we fail to detect lane lines, this is dangerous and we should try to interpolate the lines using past data, and we could create different thresholding parameter sets to represent from `relax` to `strict` to catch lane lines better in varying conditions


```python

```
