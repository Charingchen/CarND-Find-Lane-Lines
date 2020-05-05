# **Finding Lane Lines on the Road** 

## Project Writeup 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/Grey_output.jpg "Grayscale"
[image2]: ./test_images_output/Canny_output.jpg "Canny"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline logic consisted 


First, I converted the input images to grayscale, then I preform addtional gaussian blur to smooth out the image by using a kernal size of 3.

Now, I use Canny transform using `low_threshold = 30` `high_threshold = 150` to extract the edge points.


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]  ![alt text][image2]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
