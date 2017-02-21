[//]: # (Image References)
[image1]: ./examples/car_notcar.png
[image2]: ./examples/HSV_HOG.png
[image3]: ./examples/sliding_windows.png
[image4]: ./examples/vehicle_detected.png
[image5]: ./examples/example_output.png
[image6]: ./examples/example_output.jpg
[video1]: ./project_video.mp4

---
# UDACITY Vehicle Detection Project 

## The Goal of this Project

The goal of this project is to write a software pipeline to identify vehicles in a video from a front-facing camera on a car. 

## Summary

In summary, I complete this project by: 

1. Extract the features (bin spacial, color hist, HOG) of training data; 
2. Combine and scale the features, then train the data with linear SVM; 
3. Determine area of interest and appropriate window sizes, and then apply slide window; 
4. Search the windows of each frame, extract the features of all the windows, then predict if the vechile is in the windows using the SVM classifier; 
5. Use heat map and label to reduce false positive

The result is shown in the video 'project_output.mp4' 

The initial project repo can be found [here](https://github.com/udacity/CarND-Vehicle-Detection.git)

## Feature Extraction

The code for this step is contained in the IPython notebook: 'Vehicle_detection_color_space'.  
I started by reading in all the `vehicle` and `non-vehicle` files.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `HSV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

I tried various combinations of parameters and decided to use L channel of HLS color space, and `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` 
It turns out all 3 channels of RGB, V channel of HSV, L channel of LUV, L channel of HLS, Y channel of YUV, Y channel of YCrCB all have resonalbe performance. 

##Training

I trained a linear SVM using bin_spacial, color_hist, and HOG features. I also calibrated the classifier to enable the probability output. The code is in section: 'use linear SVC to train' of the IPython notebook: 'Vehicle_detection'

## Searching & Prediction

I decided to only search the bottom half of the image. The windows have 12 rows, 4 different sizes, increasing sizes when approaching the bottom of the image, and an overlap of 0.8.  the code is in section: 'slide windows' of the IPython notebook: 'Vehicle_detection'

an example image below:

![alt text][image3]

Ultimately I searched on 4 scales using L-channel HOG features of HLS space plus spatially binned color and histograms of color in the feature vector. Only predictions with over 65% probabilities are considered positive. 

## Reduce False Positive

I recorded the positions of positive detections in each frame of the video. Only predictions with over 65% probabilities are considered positive. From the positive detections I created a heatmap and with threshold to identify vehicle positions. I change the structure of the label function so that diagonal pixels are treated connected. I then constructed bounding boxes to cover the area of all the labels detected.  

Here's an example result showing the heatmap, label image and bounding boxes overlaid on a test image:

![alt text][image5]

Some more examples on the snapshots of the project video footage:

![alt text][image4]

## Potential Problems & Ways to Improve

The pipeline is likely to fail when the angel shifted significantly in the video, or there are many overlap of cars in the field of view (the pipline cannot tell different vehicles from each other).

Several ways to improve the pipeline: 1. Store the decetion results in the last several frames to further remove false positive and to produce more stable bonding boxes; 2. to increase the speed, I can extract HOG features just once for the lower half of each frame of video and subsample that array for each sliding window; 3. apply perspective transform; 4. use neural networks to train the training set.
