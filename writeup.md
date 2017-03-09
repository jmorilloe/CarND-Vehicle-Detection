**Vehicle Detection and Tracking**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier.
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)

[image1]: ./output_images/image1.png "Pipeline"
[video1]: ./processed_project_video.mp4 "Video"


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points

###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  


###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The method for this step (get_hog_features) is contained in the third code cell of the IPython notebook located in "./Vehicle_Detection.ipynb".

Firstly I loaded the datasets for vehicle and non-vehicle images. Then I played I with different color spaces (RGB, LUV and YCrCb) and different skimage.hog() parameters (orientations, pixels_per_cell, and cells_per_block).

####2. Explain how you settled on your final choice of HOG parameters.

I finally chose this set of parameters by trial and error:

color_space = 'LUV'
orient = 9  # HOG orientations
pix_per_cell = 8 # HOG pixels per cell
cell_per_block = 2 # HOG cells per block
hog_channel = 'ALL' # Can be 0, 1, 2, or "ALL"

YCrCb color space was quite promising but I finally went for LUV with good results.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

My SVM classifier is trained in cell 7, where I extract features from the datasets, then scaling and randomly splitting the data. I leave 20% of the samples for testing.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search. How did you decide what scales to search and how much to overlap windows?

To return the list of all possible windows to search on I used the multi_slide_windows method (code cell 9). Instead or resizing input image for various searches I simply changed between various sizes to try to find cars at different distances. Then, this list is passed to the search_windows method (code cell 5) to find the cars in these windows. After this I color the windows in which something is detected (add_heat method, code cell 8), the more colored ("hotter") an area is the more detections that happened in that area. Finally I apply thresholds (apply_thresholds method, code cell 8) to remove false positives, use scipy.ndimage.measurements.label() to find the cars in the hottest areas and draw the final bounding boxes (draw_labeled_bboxes method, code cell 9).

####2. Show some examples of test images to demonstrate how your pipeline is working. What did you do to optimize the performance of your classifier?

The following image shows the steps taken to identify the cars.

![alt text][image1]

The factor that contributed the most to improve the performance of the classifier was the color space. I started with all possible hog channels, spatially binned color and histograms so I had plenty of features available but they were useless in RGB color space.


###Video Implementation


####1. Provide a link to your final video output. Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)


Here's a [link to my video result](./processed_project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

As commented in a previous point, I used the apply_threshold method (code cell 8) to identify only the hottest areas that can contain a car. Then I use scipy.ndimage.measurements.label() to find the cars in the these areas and draw the final bounding boxes (draw_labeled_bboxes method, code cell 9).


###Discussion


####1. Briefly discuss any problems / issues you faced in your implementation of this project. Where will your pipeline likely fail? What could you do to make it more robust?

I got stuck for some time due to using the LUV color space and the transform_sqrt parameter = True of the hog method at the same time. Apart from that this project could be completed using most of the methods we learned during the lessons. It could be improved in several ways, such as applying the different sizes of the windows searches to different areas of the image, like only looking the smaller sizes near the horizon. Averaging the detections over a few frames could also be attempted.
