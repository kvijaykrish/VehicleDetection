---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* I also apply a color transform and append binned color features, as well as histograms of color, to the HOG feature vector. 
* For these first two steps I also normalize the features and randomize a selection for training and testing.
* Implement a sliding-window technique and use the trained classifier to search for vehicles in images.
* Run the pipeline on a video stream (started with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_windows.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video_out_roi.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

###Histogram of Oriented Gradients (HOG)

####1. Here I explain how (and identify where in the code) I  extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook 

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes.

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `HLS` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image1]


####2. Aproach on how I settled on the final choice of HOG parameters.

I tried various color sapce combinations of parameters. Initially started with the RGB color space and single channel.
The RGB color space dis not detect the white car. Then I tried out the YCbCr color space. this had many false positives.
Then I tried the HLS color space with HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`and biz size of 32. This provided good results.

####3. Training a classifier using the selected HOG features and color features.

I trained a linear SVM using a combination of following features: Color features, spatial features, HOG features.
1. The car and notcar images were read
2. The classification lables were updated
3. The images were scaled and normalized
4. The features were extracted and concatenated
5. A liner SVM was trained with these features

###Sliding Window Search

####1. In this section I will describe how I implemented a sliding window search and how I decided what scales to search and how much to overlap windows.

I decided to implement a Region of interest to avoid too many false positives.
Then selected three scales of 96x96, 128x128 and 192x192
The first two being small windows I selected 50% overlap
for the third window size of larger size 192x192 I used 80% overlap
I then used sliding window to search the ROI for each scale size

![alt text][image3]

####2. Here I show some examples of test images to demonstrate how your pipeline is working and expliain what I did to optimize the performance of your classifier.

Ultimately I searched on three scales using HLS 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  My pipeline performs reasonably well on the entire project video (there are somewhat wobbly or unstable bounding boxes however I was able to identify the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out_roi.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap for diffrent frames, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video.

### Here are four various test iamges and their corresponding heatmaps and bounding boxes:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap on the test images:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame:
![alt text][image7]



---

###Discussion

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

I see that sometimes the white car was not detected in the grey background. This could be because of the color space.
Presently the ROI is limited. The false positives could be eliminated by better averaging techniques throught multiple frames.

