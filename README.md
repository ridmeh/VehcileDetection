## Vehcile Detection
### Project to identify and mark moving cars on a given mp4 video. Detection is done frame by frame and video is reassmbled.
---

**Vehicle Detection**
Just like any image detection we have option to use CNNs. This project is focused primarily use features extracted via HOG and train a Linear SVM classifier

Following are the steps to achieve the outcome:

*1 Collect lot of data : in our case we collected lot of Car and Non-car images which are related to road

![picture](car_nocar.png)

*2 Analyse data: Check distribution of data. ML is all garbage in , garbage out. Quality of data is very important.Hence, it is very importatn we anaylze its distribution and what features we can extract. Data should be sufficiently distributed for randomizing selection for training and testing.

![picture](normalized_feature.png)

*3 Features: In our case, we are recommended to use Histogram Of Oriented Gradients (HOG). Following image was generated with HOG parameters of
orient = 9
pix_per_cell = 8
cell_per_block = 2

![picture](hog_vis.png)
I experimented to check find the best ones that can identify cars.
orient = 1
pix_per_cell = 5
cell_per_block = 3

![picture](orient1.png)
After trying various options, I prefered to use RGB and (orient = 9, pix_per_cell = 8, cell_per_block = 2) for my first run.

*4 Classifier : We will be using linear SVM
I trained the linear SVM with total of 4932 features - Spatial, Hist and HOG combined. Data set was randomized and split with 80/20 rule for training and testing. It achieved 98% accuracy. 

*5 Pipeline : Given a image, generate heat map that can be used to create bounding rectangle 
Created a pipeline for

https://youtu.be/uamuuHZaQvo


## 1 Data Collection: 

Vehicle and Non-vehicle

https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/vehicles.zip

https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/non-vehicles.zip

http://www.gti.ssr.upm.es/data/Vehicle_database.html

http://www.cvlibs.net/datasets/kitti/

https://github.com/udacity/self-driving-car/tree/master/annotations



## 2 Data Analysis: 
Data for var



## 2 Challanges:
1. HOG Feature selection

3. Timing: Getting accuracy with-in limited time.

## 2 Improvements: 
1. Sliding Window : Experiment if it can be used with other objects on road on given image. Like identify Trees, Pedestrians, Trucks etc. 
2. Real-Time: Experiment if we can use this on real time basis. How much lag would it be before cars are getting recogonized.

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (or in lines # through # of the file called `some_file.py`).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:


I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and...

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using...

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search random window positions at random scales all over the image and came up with this (ok just kidding I didn't actually ;):

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

