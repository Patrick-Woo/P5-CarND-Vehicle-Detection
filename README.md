##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/img1.png
[image2]: ./output_images/img2.png
[image3]: ./output_images/img3.png
[image4]: ./output_images/img4.png
[image5]: ./output_images/img5.png
[image6]: ./output_images/img6.png
[image7]: ./output_images/img7.png
[video1]: ./output_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  



###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first part of my jupyter notebook(CarND-Vehicle-Detection.ipynb).

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `HLS` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and decided to use color space YCrCb, all channels, orient=8,pixels_per_cel=8,cell_per_block=1. 

I chose these paramters as I used grid search with svc classifier and found these parameters can present the best accuracy on test set.

![alt text][image3]

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM in the part 1.3 in my code. I only use the hog features as it can provide excellent test accuracy say more than 0.975. Of course, I scale the features to keep them in the same range, and then use test/train set split. I train the data firstly and get the SVC clf and predict labels on test set to get the score, which is the key standard for whether this is a good classifier.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

Sliding window search is in part 3 of my code. I use find_cars_multiple function to feed me bboxs.

First, I cropped just the interest region. Then sliced the image in small frames, resized it to the right size (64x64), and applied the classification algorithm.

![alt text][image4]

then I use the find_cars(modeifed version from the lesson function.) to perform Hog Sub-sampling Window Search method to gain bboxs and drawed image.

![alt text][image6]

The car can appear in different sizes. Then, I apply different windows sizes over the image. In the end, I used following scales.

scales = [1.1,1.4, 1.8, 2.4, 2.9, 3.4] 

As it is expected bigger images near the bottom of the image and smaller images in the top of it, I divided the image frame in two, with smaller windows in the upper half, and bigger ones in the lower.


####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features. Here are some example images:

![alt text][image5]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:



### Here the resulting bounding boxes and overlaped heatmaps are drawn onto the last frame in the series and the heatmaps:

I only pickup the heatmap range that beyond the threshhold.

![alt text][image7]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I found that in this project I set too many parameters manually(compared with deep learning methods, millions of paramters can be found by CNN and DNN algorithmns), like scales = [1.1,1.4, 1.8, 2.4, 2.9, 3.4] , and this will make my code less generilised and less precised. 

Also, when it comes to hog parameters finding, I used grid search to get find the best paramters, as I have to try every possible combox, it is really time-consuming. I hope there is other approach to lower the time, but failed.



