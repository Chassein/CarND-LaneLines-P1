
# **Finding Lane Lines on the Road** 

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.


I decided to build two different pipelines depending on wheter I need to detect the lane lines from a single image or from a video. The pipline for the video uses the information about the lane lines found on the last frame. At the beginning of the video I use the same methods as for finding lane lines from a single image. 

The following preprocessing steps are part of both piplines:
* Convert the image to a grayscale image
* Blure the image with a gaussian kernel
* Use canny edge detection to find pixels with high gradients
* Define a mask to ignore all lines not in front of the car
* Use the hough line transformation to find lines from the pixels with high gradients
* Find all pixel coordinates which are marked by the hough lines

If I need to find lane lines from a single image, or if the video is at its first frame, the next steps are:
* I split the pixel coordinates found in the last step into left and right coordinates
* I use the RANSAC algorithm to fit one line through the pixel coordinates on the left and one line through the pixel coordinate on the right
* To improve these two lines, I choose a set of pixels which is close to these lines and fit a line through these points using polyfit
* Last, I combine the found lines with the original image

If I need to find lane lines for a video frame, the next steps are:
* I use the lines defined for the last frame to define two sets of pixels which are close to these lines
* If a set contains too few pixels I stick with the old line, otherwise:
* I fit one line through each set using polyfit
* If the line changes to fast from one frame to another frame, I stick with the old line
* Last, I combine the found lines with the original image

Further, I modified the hough_lines function to return only lines which are not to close to horizontal lines. Since lane lines shouldn't be horizontal.


### 2. Identify potential shortcomings with your current pipeline

I experienced the following problems with my pipeline
* For the challenge video file my preprocessing routine was not able to correctly find the pixels which correspond to the lane lines, if the road is shaded.
* If one is unlucky, the lines found by the RANSAC algorithm are not close to the real lane lines.


### 3. Suggest possible improvements to your pipeline

I had several ideas to improve my pipeline. Unfortunately, I wasn't able to implement them before the deadline:
* One should switch x and y pixels to avoid lines with infinte slope
* The RANSAC algorithm should select more than two points to avoid badly conditioned fitting instances
* One should spent more time on the preprocessing routine to identify more clearly the pixels corresponding to lane lines.
* Splitting the video frame into a left and right half to find the pixels corresponding to the left and right line is not very sophisticated. Unfortunately, I had no time to test more sophisticated methods, like the 2-center algorithm for example.
* In the current version of the video pipline, lane lines are taken from the last frame if the actual frame contains no meaning full pixels to find lanes lines. This is clearly not the best option. Instead on could use the lane lines from the last frame to identify regions in which the preprocessing routine should spent more effort to search for pixels correspdoning to lane lines.
* In general the routine could be improved by automatically adjusting the parameters of the preprocessing routine if one finds that to few pixels where found corresponding to lane lines.
* A straithforward improvement is to use parabels instead of lines which are fitted to the lane lines
