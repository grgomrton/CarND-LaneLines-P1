# Finding Lane Lines on the Road - Assignment Entry

This repository contains my solution for the Finding Lane Lines assignment of the Self-Driving Car Nanodegree program at Udacity. 

![An example output][cover_image]

### Building the environment
- Download and install the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit)
- Clone this repository

### Executing the pipeline

- Switch to _carnd-term1_ environment and start Jupyter:  
  `$ source activate carnd-term1`  
  `$ cd ~/CarND-LaneLines-P1`  
  `$ jupyter notebook`  
- Navigate to `http://localhost:8888/`, open the _P1_ notebook and click on `Kernel / Restart & Run all`
- The pipeline should execute on the test images, the input videos and on the challenge video

## Algorithm description

The pipeline analyses a color input image and returns a color image with the identified lane lines drawn onto it.
### Process
From the input image two edge image is created:  
`edges_from_shape = greyscale > gauss-blur > canny`  
`edges_from_color = select white pixels U select yellow pixels > gauss-blur > canny`  
The final edge image is the combination of the two:  
`edges = edges_from_shape U edges_from_color`  

![Process of finding edges][edges]
_The edges from shape information, the edges from color segmentation and the final edge image_

A Hough-line detection is performed on the final image. The output segments are categorized into left and right sets based on their angle:  
`left_segments = segment in segments : -90° > segment.angle > -20°`  
`right_segments = segment in segments : 90° > segment.angle > 20°`  

The segments are converted to `XTheta` representations, which defines lines by their:
- X-intercept: the point where the line intersects with the x-axis
- Theta: the signed angle between the line and the x-axis
- Weight (optional): the weight associated with the line

The left and the right lanes are the average of the lines in the respective sets. The lines are weighted by the length of their originating segments.  
`left_lane_line = XTheta(avg_x_intercept, avg_angle) of left_lines`  
`right_lane_line = XTheta(avg_x_intercept, avg_angle) of right_lines`  

For still images the final lane line is this average line. 

![An image with the entire left lane line drawn][left_lane]
_The entire left lane line drawn onto an input image_

For videos a queue of the identified lanes is used to improve the precision and reduce jitter in the result. For videos, the final lanes are:  
`averaged_left_lane_line = XTheta(avg_x_intercept, avg_angle) of left_lane_lines in queue`  
`averaged_right_lane_line = XTheta(avg_x_intercept, avg_angle) of right_lane_lines in queue`  

![Snapshots from the output video yellow.mp4][yellow_snapshots]
_The pipeline executing on an input video_

![Snapshots from the output video extra.mp4][challenge_snapshots]
_The pipeline executing on the challenge video_


--
Made for the Self-Driving Car NanoDegree Program at Udacity

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


[//]: # (Image References)

[cover_image]: ./documentation/cover_image.png
[edges]: ./documentation/edge_progress.png
[left_lane]: ./documentation/full_left_lane_drawn.png
[yellow_snapshots]: ./documentation/snapshots_from_yellow.png
[challenge_snapshots]: ./documentation/snapshots_from_extra.png

