# GCP-Detection-For-Drones
A Ground Control Point (GCP) is a physical marker placed on the ground, which can be identified from the drone images. These GCPs then need to be detected in images and marked. The GCP positions are then fed into the photogrammetry software in order to give the output absolute position. When done manually, the process of detecting the GCPs is extremely cumbersome.

I’ve developed a program using Image Processing algorithms to detect GCPs.

Results from above contain false positives along with detected GCPs. To remove that, I’ve used deep learning to classify between valid and invalid GCPs with 90% accuracy.

