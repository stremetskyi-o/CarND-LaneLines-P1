# **Finding Lane Lines on the Road** 

## Project Writeup

---

**Finding Lane Lines on the Road**

The goals/steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Pipeline

Implemented pipeline consist of 6 steps:

At first, the image converted to *grayscale*, which allows manipulating on a single value for each X and Y of the source
and simplifies following calculations.
 
Secondly *Gaussian Blur* is applied, it reduces the amount of noise which can be identified as edge and line.
I have found that using value 5 or 7 does the best job as it successfully reduces noise and leaves enough detail for
the lines on the far side of the road.  

Next step is running *Canny edge detection* algorithm. I have started with setting high and low thresholds to 200 and
began lowering it until I found the value which detects at least some pixels of every line I was interested in, 
then I minimized low threshold to tens and began playing around to show the most detail for lines and none of the other
edges. I stopped at value 30, which roughly gives a *1:3* ratio as suggested by John F. Canny.

After edges were detected, it was possible to apply *region of interest* mask, this allows to detect only relevant lines
in the next step, but applying mask before edge detection would give an error as the edge around mask would be detected
as well. Mask was selected with some margins on both of the sides as lines tend to fluctuate and won't be in the same
place every time.

Than *Hough transform* line detection is applied, I came up with a minimal grid with values 1 and Pi/180 to ensure the
lines are straight, I also set minimal intersection count to 5, minimal length to 6 and the maximal gap to 5. Some 
measurements were received by measuring source images pixel by pixel.

Next step calculates one line on each side based on the lines from the previous step. To do this, I have calculated
the slope of each line by using `m = (y1 - y0) / (x1 - x0)` equation and split them to 2 different groups - with positive
and negative slopes, which are for left and right lines. Also, I have filtered out nearly horizontal and nearly vertical
lines which aren't the lines I am looking for. For each group mean slope and its standard deviation were calculated.
Based on these values I have selected relevant lines whose slopes lies in `[mean - std, mean + std]` interval. 
Next, the array of the lines was transformed to an array of coordinates and sorted by Y values descending. Here I have
decided to calculate the approximate linear equation of `x(y)` function. Using Y as a parameter allows to span new line
more adequately as most irrelevant lines are found on the sides of the real ones - on the X-axis. Line equation was
calculated using a least-squares algorithm from the NumPy package. For each new line, start and end points were
calculated using line equation parameters as well as minimal and maximal values of the source lines on the Y-axis.

After having this result, the last step draws the calculated lines on the source image and writes it to the 
`test_images_output` folder.


### 2. Potential shortcomings

The first problem is that some detail of the far objects can be removed by applying the blur. Another shortcoming is
detecting short line segments on the shadow edges. The next fault is that the result differs under different light 
conditions. The last shortcoming is that lines only detected where they are straight, so the road curvature is not known.

### 3. Possible improvements

To improve the lane lines detection, different values for Gaussian blur, Canny edge detection, and Hough transform 
should be used for different parts of the image. Alternatively, image perspective can be changed to show an aerial view
of the road. The possible way of minimizing shadow detection is image preprocessing to adjust contrast/brightness or 
using separate RGB channels for different features as well as a different color system like HSV. To detect curves,
linear approximation can be replaced with the polynomial.