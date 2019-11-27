# GCP-Detection-For-Drones
A Ground Control Point (GCP) is a physical marker placed on the ground, which can be identified from the drone images. These GCPs then need to be detected in images and marked. The GCP positions are then fed into the photogrammetry software in order to give the output absolute position. When done manually, the process of detecting the GCPs is extremely cumbersome.

I’ve developed a program using Image Processing algorithms to detect GCPs.

Results from above contain false positives along with detected GCPs. To remove that, I’ve used deep learning to classify between valid and invalid GCPs with 90% accuracy.

# Details :

# Research and Brief:

I’ve applied image processing algorithms to extract positions of GCPs in the image. But
along with correct positions, it also gives some false positives. To remove false positives, we
need to apply deep learning only on those patches extracted above (instead of the whole
image), to rightly classify between markers and false positives.
For deep learning, I’ve used 3 layered architecture, which is classifying images with training
accuracy of 90% and testing accuracy of 89.63%.

# Procedure:

(Note - All the procedures are explained in more detail in Ipython notebooks)
GCP Detection (Image Processing part)
1) Preprocessing on the image -
- Resize
- Convert into grayscale
- Smoothing (by bilateralFilter)
- adaptive thresholding
- Morphological Operation (2 times dilation and 1-time erosion with 3*3
mask)
2) Extract Contours:
- Approximate each contour whose area <= 50
- Find minAreaRect
- Crop the bounded Rectangle (Rotated Rectangle) by rotating the image
  - Take its complement and again find contours.
  - For any L shaped, the contour must be a 4 sided square/polygon
    (4 corners) (#corners are varying between 2 - 6 due to a difference
    in altitude)
  - Check if the contour is of a certain shape
  - Calculate the area
- If abs(width-height) <= 10
  - 4 sided contour is calculated by cropping the rounded rectangle
    from the complement of the original contour
- Used Affine transformation
- Rotate the whole image, then crop the rectangle
  using affine wrapping
(2) If area difference less than 35
- Create a binary thresholded image
  - Thresholding value is calculated using
    histogram analysis
- Count the number of white pixels of the size of
  bounding rectangle from the above binary image
- If number > 0, pass the cropped bounding
rectangle to the machine learning model, if the
output is 1, then store its (x,y) coordinates w.r.t the
image and draw a blue bounding rectangle,
otherwise if the output is 0 then draw a red
rectangle.
# Model Training (DetectionModel) :
- Train Feed Forward Neural Network with 3 layers
  - input layer - 1200 * 10 * 10 = 120000 neurons¶
  - 1st hidden layer - 70 neurons - relu activation function
  - Dropout with 0.5 probable dropouts to overcome overfitting
  - 2nd hidden layer - 40 neurons - relu activation function
  - 3rd hidden layer - 45 neurons - relu activation function
  - Output Layer - softmax with 2 neurons (0 and 1)
  - Train model using ADAM OPTIMIZER
  - Loss Function - sparse_categorical_crossentropy
- Accuracy
  - Training - 90%
  - Test - 89.63%
# Preprocessing of the dataset (Data Augmentation):
- Resize
  -all the cropped images to 10*10
- Data Augmentation
  - Since data was less, 228 patches with GCP and 351 false patches, we
need to augment our data
  - 228 -> 851 true images & 351 -> 601 false images
  - Used - rotations, flip left, flip bottom
- Created pickle files to store the dataset
- Dividing dataset into 20% test and 80% training data
# Running Instructions :
1) Open notebook GCPDetection.pynb.
2) Put path of the folder (which contains your images) in the finalResult() method
- Eg. final result('./AssignmentDataset')
3) Run all the cells.
4) After each image following outputs will be shown:
- Image with marked rounding box (press any key on the keyboard to move to next
image)
- Blue boxes - Model predicting true value
- Red boxed - Model predicting the false value
5) (x,y) locations of the bounding boxes

Note: The output still contains false positives which can be
removed by training our model with more data. The above
algorithm is not classifying GCPs that are merged with some
another white patch.

