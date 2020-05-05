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

First, I converted the input images to grayscale, and then I perform additional gaussian blur to smooth out the image by using a kernel size of 3.

Now, I use Canny transform using `low_threshold = 30` `high_threshold = 150` to extract the edge points. Here is the Canny transform result of "whiteCarLaneSwitch.jpg"
![][image2]


Next is to define the mask of interest. Instead of using a fixed number, I used a Trapezoid mask and set the mask height as 3/4 of the total image height. I set top width as 150. I also included a 50 pixel offset at bottom of trapezoid using bottom two points as `(0+corner_offset,imshape[0])` `(imshape[1]-corner_offset,imshape[0])`
```
    #Record the shape of the input image
    imshape=image.shape
     
    # Define the mask parameters #330 for white and yellow
    img_y_mid = imshape[0]/2
    
    mask_height = int(img_y_mid + img_y_mid*0.25)
    mask_top_width = 150
    corner_offset = 50
    img_x_mid = int(imshape[1]/2)
 
    first_point = (img_x_mid - mask_top_width/2 , mask_height)
    second_point = (img_x_mid + mask_top_width/2 , mask_height)
    
    # Define the mask vertices

    mask_vertices = np.array([[(0+corner_offset,imshape[0]),first_point,second_point,
                               (imshape[1]-corner_offset,imshape[0])]],dtype=np.int32)

```

The `masked_edge` is the input of the hough line function. I used below parameters to run hough transformation:
```
    rho = 1 # distance resolution in pixels of the Hough grid
    theta = np.pi/180 # angular resolution in radians of the Hough grid
    threshold =  10 # minimum number of votes (intersections in Hough grid cell)
    min_line_length = 10 #minimum number of pixels making up a line
    max_line_gap = 30 # maximum gap in pixels between connectable line segments
```

At the end of the `hough_lines`, I called my improved draw line function `cum_ave_lines`.
#### Improved Draw Lines Comparing Previous Results
`cum_ave_lines` calls for two more function called `process_lines_ave` and `sort_lines_compare_ave`. 

`cum_ave_lines` call `sort_lines_compare_ave` first, where the output of `hough_lines` is first fitered out any lines that have a smaller slope of 0.5, and then sort lines base on their positivity into left and right lane lines. 

Right after, I compare the current slope of the line to the previous left or right lane slope to see if this slope is within the tolerance of the last slope. 
If not, it is not counted toward the next averaging calculation. 
After this sort, I check for an empty result. If the previous step discarded all lines, I assign the previous slope to the current one to handle the empty array fitLine error.

And then `sort_lines_compare_ave` returns the slope and one point for each extrapolate lines. Now back in `cum_ave_lines`, I check the output result of `sort_lines_compare_ave`. 
If it returns a 0 value slope, draw previous lines instead. If not, call `process_lines_ave` to average current extrapolate results with the previous result. 
In `process_lines_ave`, only the previous 20 slopes and bottom X coordinate are averaged by using np.mean and then average again with the current extrapolate result. This is the final result slope and x bottom coordinate. And then draw the final lines to the image.

`process_lines_ave` is called twice for both lanes in the `cum_ave_lines`


### 2. Identify potential shortcomings with your current pipeline

**Known Bugs of current pipeline**
1. If the current group of lines are all outside the previous slope tolerance, the extrapolate result would be based on the last result. If this keeps happening for a couple of iteration, the extrapolate result would be inaccurate.
2. If the first 20 iterations are not clean and stable images, the whole pipeline will not work.
3. The pipeline would disappear after a couple of iteration in the challenge video.

**Shortcomings**
1. The current pipeline is not able to deal with a noisy result of hough lines. If there are too many inaccurate lines from the hough transformation or too little point detected by Canny detection, the pipeline average would be not stable.
2. This pipeline is not suitable for sharp turning lane detection due to the tolerance setting is set to reject a slope change around 10%
3. This pipeline is not ideal if there is a car in front. Canny detection and hough transformation would include the vehicle and cause inaccurate pipeline reading
4. `sort_lines_compare_ave` is not designed to handle a noisy output of hough lines.  With a fixed hough and canny parameter setting, this logic only works in an ideal environment.




### 3. Suggest possible improvements to your pipeline

A possible improvement is to debug the challenge video frame by frame and monitor variables to see what is causing the lines to disappear after a couple of iteration. 

Another improvement is to set up a more sophisticated feedback logic to adapt the current slope and bottom X calculation. Instead of averaging current with all previous results, have an active negative feedback loop to compensate for slope change.

The final improvement could be turning hough and canny parameter to every different video input. This would ensure the output of houge line be less noisy for the average to work better. 

