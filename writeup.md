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

First, I converted the input images to grayscale, then I preform addtional gaussian blur to smooth out the image by using a kernal size of 3.

Now, I use Canny transform using `low_threshold = 30` `high_threshold = 150` to extract the edge points. Here is the Canny transform result of "whiteCarLaneSwitch.jpg"
![][image2]


Next is to define the mask of interest. Instead of use a fixed number, I used a Trapezoid mask and set the mask height as 3/4 of total image height. I set top width as 150. I also included a 50 pixel offset at bottom of trapezoid using bottom two points as `(0+corner_offset,imshape[0])` `(imshape[1]-corner_offset,imshape[0])`
```
 Record the shape of the input image
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
    # Draw a edged image only include the masked region
    masked_edges = region_of_interest(edges,mask_vertices)
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
`cum_ave_lines` calls for two more function called `process_line_ave` and `sort_lines_compare_ave`. 

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...