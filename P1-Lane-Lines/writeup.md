# **Finding Lane Lines on the Road** 

## Writeup 
---

[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve_avg.jpg "Grayscale"
[image2]: ./test_images_output/solidYellowCurve_avg.jpg "Grayscale"
[image3]: ./test_images_output/challange.jpg "Grayscale" 

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 steps. At first, I used 6 steps, however, thanks to the challenge I added a step improves the detection by ignoring edges that are not white nor yellow. First, I converted replaced all yellow pixels in the image to white, then that I have the pixels that interest me in white, I filter all other colors having a black and white image. Because this last image is in the RGB format I then convert it into grayscale. Then, in the fourth step, I smooth the image applying a Gaussian Noise kernel with cv2.GaussianBlur function. Later I apply Canny edge detector. After applying the edge detector, I define a polygonal region of interest and obtain an image that is masked by it. Finally, with the help of the Hough transform I find lines in the image and based on these and the use of the draw_lines() function the lane lines are detected and annotated on the input video. So, to summarize, the steps taken where:

1. Replace yellow pixels for white ones. 
2. Filter the image to keep only white pixels.
3. Convert RGB image into grayscale. 
4. Smooth the image. 
5. Use canny edge detection.
6. Define a region of interest and mask the image accordingly. 
7. Detect and get lines with Hough transform function. 
8. Use draw_lines() function to annotate the video. 


In order to draw a single line on the left and right lanes, I modified the draw_lines() function as follows. Worth noticing that I copied the function and changed its name to separate_lines(). First, I divided the lines obtained with the Hough transform function cv2.HoughLinesP() into right and left ones based on their slope. Positive slope means that they are right lane lines and negative, left lane lines. Then I averaged the lines (starting and end point pairs) of each group (right and left) and with the result I obtained the parameters of the line equation: slope and bias. With these parameters, I was able to extrapolate the lane lines from a desired initial y0 coordinate to a final one yf. To stop the flicker resulting from only applying the previous step to get line annotations I used a moving average of the slope and bias to later extrapolate the line considering the information from the 20 previous frames. Once the extrapolated lines are calculated they are drawn in the image. So, to summarize, the steps taken to change the draw_lines() function where:

1. Divide right and left lines based on their slopes. 
2. Average the starting and end points of each line. 
3. Get a average slope and bias for each left and right lanes.
4. Get the moving average of the slope and bias from the last 20 frames. 
5. Extrapolate the lines with the slope and bias and the intersection with desired initial and final y coordinates. 

A sample of the result for each of the videos, in order (white lanes, yellow lane, challenge), are shown below.  

![alt text][image1]

![alt text][image2]

![alt text][image3]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be that when the lane to be detected falls outside the region of interest, then the pipeline would not be able to detect any lanes that are not included inside that region. 

Another shortcoming could be that if the size of the images in the video change, then the accuracy of the line annotations could be affected. The code was modified so that the x and y coordinates used for the extrapolation adjust to the image size and this works for the three videos used, however, if the proportion of the location of the lanes changes then a problem would arise. Other similar shortcoming might happen with the placement of the region of interest, in this case, the apex point is static. 

One important shortcoming would happen when a white or yellow object, such as a car with those colors, gets too close to the self-driving car. In this case, the pipeline would detect those objects as lines because they would enter the region of interest and would not be filtered out. This may cause the resulting lines for lane annotation would be place in a very low accurate location.  


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to consider objects that may get really close to the self-driving car and enter the region of interest defined in the pipeline. 

Another potential improvement could be to let the program set the region of interest according to the size of the frames, this way, when the video resolution changes, the line annotation placement would not be much affected. 

One possible final improvement would be to make the program able to tell if a line or a small group of lines, detected with Hough transform has a slope that is very different from the rest and take it out the calculation of the average slope. This may perhaps help to make the results more accurate.

