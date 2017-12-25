# **Finding Lane Lines on the Road** 

## sugang

### The objective of this project is to identify lanes from input images / videos

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

- Make a pipeline that finds lane lines on the road
- Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg "Lane detector"
[image2]: ./test_images_output/merged_line.png "Merged Lines"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

#### The current pipeline consists of the following steps:

1. Convert Image to Greyscale
2. Balance Image histogram using (cv2.equalizeHist)
3. Apply Gaussian Blur (w/ kernal size = 5)
4. Use threshold filter to retain only bright areas in the image. The white threshold is set to be 85th percentile of image brightness
5. Use a quadrilateral region mask to crop out areas which are less likely to contain road
6. Use Canny Edge detection to find edges
7. Use Hough line transformation to fine lines
8. Overlay

The following is a result of the lane detector:
![alt text][image1]

Other images w/ lane mapping can be found in folder *test\_images\_output*

#### In order to draw a single line on the left and right lanes, I modified the draw_lines() function with the following heuristic:

1. Compute the slope of everyline
2. Allocate lines into two buckets - those w/ positive slope (left) and those w/ negative slope (right)
3. Run a regression to obtain 'averaged' slope
4. find the intersection points of the two regression lines to the bottom of the screen, these are two bottom anchor points
5. Find a mid point in screen w/ some heuristic (1/3) of the screen to mark the upper ends of the regression lines

Here's one exmaple: please refer to the jupyter notebook for more details. The processed videos can be found in *test\_videos\_output*

![alt text][image2]


### 2. Identify potential shortcomings with your current pipeline

The most significant vulnerability of the current methodology is that it's heavily depedent on parameters and heuristics and may not be very generailizable and reliable. The lighting condition (day / night / sunny / cloudy) may impact the performance of the lane detector. Furthermore, shorter, dashed lanes may not be detected correctly.

I used an intutive regression method to find the left and right lanes. As shown in the results, it may not be reliable due to:
 
- Multiple left / right lanes detected in view, which will result in funky behavior of the regression line
- If side of the road contains bright spots such as sand / parallel roads, the regression line may not be reliable 
- The standard OLS regression is very sensitive to outliers, which is demonstrated by 'flickering' of the lane detector 
- This methodology is inherently susceptible to turns / curves 


### 3. Suggest possible improvements to your pipeline

- The current video processing is performed frame by frame. One way to improvement the stability of lane detection in continous video stream is to borrow recent information to reduce 'flickering'
- Depending on the camera angle and size of vehicle, road may take various portions in view (1/4 ~ 1/2). It maybe useful to first segment the road w/ computer vision / deep learning techiniques and then apply lane detection on top of the extracted portion of image.
