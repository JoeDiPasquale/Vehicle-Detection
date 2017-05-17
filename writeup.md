##Writeup 


**Project 5:Vehicle Detection**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup


###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

Code for extracting HOG and other features is in P5.ipynb. After many trial and error trainings with the classifier, I found some variables that gave me the best results.

First I loaded the vehicle and non-vehicle data sets. Then I loaded the autti dataset. I tried the pipeline with loading crowdAI dataset and the code is in the comments. I extracted hog features using extract_features().This function first creates a list named 'features'. It then iterates through all the images and does the following
1.applies color conversion
2.if spatial_feat is true then bin_spatial features are also added.
3.if hist_feat is true then color_hist features are also added.
4.if hog_feat is true then hog_features are calculated using get_hog_features function and then hog features are added.
At the end of the function it concatenates into a single feature numpy array and returns the features.
This is how I extracted features.


####2. Explain how you settled on your final choice of HOG parameters.

After much trial and error with training the classifier, I found variables that gave me the best results.
The image is converted to the GRAY colorspace. I tried several color spaces including RGB, HSV & HSL, but settled on GRAY
I generated the HOG for each color channel with 8 orientations, 16 pixels per cell, and 1 cells per block. 
I take the color histogram of each color channel and concatenate them together. I divide the histogram into 16 bins. 
Finally, I do a spatial binning after resizing the image to 16x16. This flattens the image array. 


####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

Here I also I did many trial and errors with training the classifier
First I tried with LinearSVC, the maximum accuracy i got is 0.9384
Then, I tried with LinearSVC with loss argument set to hinge. With experimentation I was able to get an accuracy of 0.9419
Then I tried with SVC which was giving me accuracy more than 0.98.so i selected SVC


###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding window approach I took is very similar to what was taught in the course.. sliding_window function takes an image,start and stop positions in both x and y,window size (x and y dimensions) and overlap fraction (for both x and y).The scale determines how large of a window size you'd like for each sliding window. A scale of one, means the sliding window matches the size of the feature input which is 64x64. A scale of two means each sliding window will be 128x128. This scale is also returned with each sliding window output in order to compute HOG features later on.

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

In order to reduce the number of false positives, the sliding windows are only chosen on the bottom half of the image.  I also made the code such that only the right half of the image is searched as left side is blocked by highway median.


### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

The final video output will be in the root folder where this writeup file will be.


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I made a HotBox class which covers joining hot boxes algorithm. We have many overlapped boxes and we need to join it around peaks to convert many overlapped boxes into a smaller amount of not or slightly overlapped ones. The idea is to take the first box (called average box) from input boxes and join it with all boxes that are close enough (for two boxes that overlap by at least 30% of area of any one of the two) After joining two boxes we need to update the average box (increasing the size to cover both joining boxes). Loop as long as we are able to join more. For left boxes repeat all procedures. As a result we may also get average box strengths and the number of boxes it was joined to.

I also have a class LastHotBox which will accumulate hot boxes for the last n frames, where n is passed as an argument to the constructor. 

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The only problem I faced is when I tried using the crowdAI data set, I was getting an error with opencv. I did try to keep the try catch block because opencv has a bug.
The following link describes the actual bug.
 https://github.com/opencv/opencv/pull/6883/commits/5bc10ef796c29563e8916ff31b5c1a262422a93f

 I also could have trained it with both autti and crowdai dataset to train the classifier better.


