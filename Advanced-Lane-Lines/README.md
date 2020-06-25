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

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image. 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

![image](https://user-images.githubusercontent.com/24778538/85175933-8c691680-b270-11ea-933b-9a89ceb3a41a.png)

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. I applied this distortion correction to the test image using the `cv2.undistort()` function. I also applied perspective transform on the chessboard images and obtained the results: 

![image](https://user-images.githubusercontent.com/24778538/85176596-15cd1880-b272-11ea-808b-ba023b19ef8f.png)

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![image](https://user-images.githubusercontent.com/24778538/85209492-63a85600-b330-11ea-8a1c-04018e4d2386.png)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

After experimentation, the following color channels and thresholds did well identifying the lane lines in the provided test images:

- The S Channel from the HLS color space, with a min threshold of 200 and a max threshold of 255, did well identifying white and yellow lane lines, but missed lane lines with shadows.
- The L Channel from the LUV color space, with a min threshold of 255 and a max threshold of 255, picked up only the white lane lines.
- The B channel from the Lab color space, with a min threshold of 150 and an upper threshold of 220, did well in identifying only the yellow lines.
- A combined binary threshold based on the three above mentioned binary thresholds is used which does a good job of highlighting both the white and yellow lane lines.

![image](https://user-images.githubusercontent.com/24778538/85209620-390acd00-b331-11ea-906a-e02ae555ad6d.png)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp_test_images`.  The `warp_test_images` function takes as inputs an image (`img`) & creates source (`src`) and destination (`dst`) points as following:

```python
    src = np.float32([[480, 480],[800, 480],
                      [1250, 720],[0, 720]])
    dst = np.float32([[0, 0], [1280, 0], 
                     [1250, 720],[0, 720]])
```

I also obtain map & inverse map using:

    M = cv2.getPerspectiveTransform(src, dst)
    Minv = cv2.getPerspectiveTransform(dst, src)
    
Finally, the warped image is obtained:

```python
    warped = cv2.warpPerspective(img, M, img.shape[::-1])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![image](https://user-images.githubusercontent.com/24778538/85225606-77a09600-b3ca-11ea-90c7-5e1cdec8cba7.png)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I implemented sliding window, search from prior & fit polynomial as below:

![image](https://user-images.githubusercontent.com/24778538/85254365-07d8ec80-b458-11ea-8776-aca4b07e8d54.png)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

This has been implemented in cells 14, 15 & 16 using the functions `measure_curvature_real()`, `warp_lane()` & `draw_result()`.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

This has been implemented in cells 17. Here is an example of the original & final image:

![image](https://user-images.githubusercontent.com/24778538/85452250-99e51000-b592-11ea-9cbe-188a82c33be2.png)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./test_videos_output/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

#### Problems / issues:
The only major issue faced is determining a binary threshold that would work in all conditions. 

#### Likely failure:
When the car passes in areas where there is shadows from the trees on the side.

#### Improvement:
To determine a binary threshold that produces good results under all conditions such as shadows, various light & weather conditions, dim lane lines etc.
