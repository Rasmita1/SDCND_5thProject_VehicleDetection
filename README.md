# Vehicle Detection and Tracking Project

The goals / steps of this project are the following:

- Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
- Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
- Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
- Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
- Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
- Estimate a bounding box for vehicles detected

This project folder contains
- utils.py (functions to extract, loading helpers)
- features.py (feature extraction and classes)
- images.py (image and window slice processing)
- search.py (holds search parameters class)
- boxes.py (windowing and box classes)
- detection.py (main VehicleDetection class that coordinates processing of images)

## Rubric Points

Here I will consider the rubric points individually and describe how each point is addressed in my implementation.
#Histogram of Oriented Gradients (HOG)

1. Explain how (and identify where in your code) you extracted HOG features from the training images. 
    The code for this step is written in extract_hog_features, which take an array of 64x64x3 images and return a set of features. 
    These are extracted in parallel and it in turn uses HogImageFeatures class.

2. Explain how you settled on your final choice of HOG parameters.

   I tried various combinations of parameters and finally set the below parameters,
       color_space = 'YCrCb' # Can be BGR, HSV, LUV, HLS, YUV, YCrCb
       orient = 9  # HOG orientations
       pix_per_cell = 8 # HOG pixels per cell
       cell_per_block = 2 # HOG cells per block
       hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"
       spatial_size = (32, 32) # Spatial binning dimensions
       hist_bins = 32    # Number of histogram bins
       hist_range = (0,256)
       spatial_feat = False # Spatial features on or off
       hist_feat = False # Histogram features on or off
       hog_feat = True # HOG features on or off
       
 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them)
    I used a linear SVC as the training accuracy is 1.0, and testing accuracy is 0.9887, and 0.009 Seconds to predict 10 labels with SVC.
    15.54 Seconds to train SVC...

 ### Sliding Window Search
 1. Describe how (and identify where in your code) you implemented a sliding window search. 
    How did you decide what scales to search and how much to overlap windows?
    
    For this project four sizes of windows were chosen - 32x32, 48x48, 64x64 and 128x128 and position at different depth perspective on     the bottom right side of the image to cover the road. The larger windows closer to the driver and the smaller closer to the horizon.     Overlap in both x,y was set between 0.5 and 0.8 to balance the need for better coverage vs number of boxes generated- currently 937.     The more boxes for a sliding window, the more calculations per video image.
    
test_video.mp4:    

![heatmaps and label](https://user-images.githubusercontent.com/28965433/35473401-58786d7c-0380-11e8-95ab-c71b6043aa8a.png)

project_video.mp4:
![heatmap_history](https://user-images.githubusercontent.com/28965433/35473402-61ffdeb6-0380-11e8-8b12-3fd9e97412c0.png)

    
### Video Implementation

1. Final video output, Vehicle detection video - 'project_video_vehicle_detection.mp4' (present in Github project repository)
2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes
I used scipy.ndimage.measurements.label function which process 3d arrays giving labels in x,y,z spaces. when using the array of heat map history as input, it labels connections in x,y,z. If a returned label box is not represented in at least 3 (heat map history max - 2) z planes then it is rejected as a false positive. The result is that a vehicle is tracked over the heat map history kept.

### Discussion
I wanted to add the lane detection in the vehicle detection project, but unable to do due to lack of time.
The pipeline would fail if vehicles were on the left hand side or centre of the car. I suspect trucks, motorbikes, cyclists and pedestrians would not be detected (as they are not in the training data).
