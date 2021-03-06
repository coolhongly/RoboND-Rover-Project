## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.
I added two more functions, color_thresh_obstacles and color_thresh_rocks to do color selection for obstacles and rock samples, respectively.
color_thresh_obstacles will select pixels that are "R < 160 && G < 160 && B < 160 && R != 0 && G != 0 && B != 0". The reason for choosing this criteria is the obstacles are generally dark, and all black pixels are generally irrevevant pixels generated by perspective transform.
color_thresh_rocks will select pixels thare are "R > 100 && G > 100 && B < 50". I found this combination by inspecting the color range of samples.

#### 2. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 
Once the navigable terrain, obstacles and rock samples are identified with the color filtering, I colored the pixels on the worldmap blue for terrains, red for obstacles and white for samples.

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.
perception_step() function is modified to reflect the identification of different objects on the worldmap. decision_step() is modified for better decision making. The details will be discussed in the next question.

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

In my implementation, the rover not only depicts different objects (terrain, obstacles, samples) on the worldmap, it is equippted with the following new abilities:
1. Move along the left of the terrain for better terrain investigation coverage. A simple trick to walk in the maze is to always walk along the left or the right of the wall. This way we won't walk the way we already walked before. The sample strategy applies for this project as well. To do this, I changed the decision.py so that the the rover will steer to 3/4 of the navigable angles rather than the mean.
2. Steer to a sample to pick it up when the rover sees one in reasonable close range. In perception.py, I set the "Rover.nav_angles" to the angles of the rock if there is a rock and the rock is within distance 100. This way in the later decision making process the rover will be navigated to reach that sample. 
3. Variable max speed for faster navigation and careful sample pickup. If the rover does not see a sample (notice this is not the sample as "near_sample" flag), it will move at a larger max speed of 2m/s, otherwise it will move at the max speed of 1m/s. I found this modification necessary, because we want the rover to move fast, but with this speed we can easily miss samlpes.
4. Get out of any stuck situation. This logic is crutial as in my experiments I see many times the rover is stuck in simulation where there are enough navigable pixels, the rover is thorttled but some rock still blocks the movement. So I added the logic in decision.py that if the rover is throttled but it is at very low speed (0.05m/s), then we steer the rover to -15 degrees. I found this logic very effective (works pretty much in every stuck situation) to get the rover out and running.

resolution: 640 x 480
graphics quality: fastest
FPS: 38



![alt text][image3]


