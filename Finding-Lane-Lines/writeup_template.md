# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road


---

### Reflection


My pipeline consisted of:
    - Converting image into grayscale
    - Applying gaussian blur
    - Using canny edge detection
    - Using region of interest to create mask image & then applying bitwise on mask & canny
    - Applying Hough Tranform line & using polyfit to determine slope & intercept. 
    - The slope is used to fit lines to original image using their average


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there is too much brightness or sunshine

Another shortcoming could be the slow speed of detection & also lane detection becoming less steady


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to determine lane lines when they bend

