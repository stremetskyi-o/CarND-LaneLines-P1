# **Finding Lane Lines on the Road** 

## Project Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Pipeline

Implemented pipeline consist of 6 steps:

At first image converted to *grayscale*, which allows to manipulate on a single value for each x and y of the source
and simplifies latter calculations.
 
Secondly *Gaussian Blur* is applied, it reduces amount of noise which could be possibly identified as edge and line.
I have found that using value 5 or 7 does the best job as they're successfully reduce noise and leave enough detail for
the lines on the far side of the road.  

Next step is running *Canny edge detection* algorithm, I have started with setting high and low thresholds to 200 and
began lowering it to found value which detects at least some pixels of every line I was interested in, than I minimized
low threshold to tens and began playing around to show the most detail for lines and none of the other edges. I stopped
at value 30, which roughly gives 1:3 ratio as suggested by John F. Canny.

After edges was detected it was possible to apply *region of interest* mask, this allows to detect only relevant lines in
the next step, but applying mask before edge detection would give an error as the edge around mask would be detected 
as well. Mask was selected with some margins on both of the sides as lines tend to fluctuate and won't be in the exact
same place every time.

Than *Hough transform* line detection is applied, I came up with really small grid with values 1 and Pi/180 to ensure the
lines are straight, I also set minimal intersection count to 5, minimal length to 6 and maximal gap to 5. Some measurements
were received by measuring source images pixel by pixel.

Next step calculates one line on each side based on the lines from the previous step. To do this i have calculated
the slope of each line by using m = (y1 - y0) / (x1 - x0) equation and split them to 2 different groups - with positive
and negative slopes, which are for left and right lines. Also I have filtered out nearly horizontal and nearly vertical
lines as they aren't the lines I am looking for. For each group mean slope and it's standard deviation were calculated.
Based on these values I have selected relevant lines whose slopes lies in [mean - std, mean + std] interval. Then array
of the line coordinates was transformed to array of all coordinates and sorted by y values descending. 
Here I have decided to calculate approximate linear equation of x(y) function. Using y as parameter allows to span 
new line more adequately as most irrelevant lines are found on the sides of the real ones - on the x axis. Line equation
was calculated using least-squares algorithm from NumPy package. For each new line start and end points were 
calculated using line equation parameters as well as minimal and maximal values of the source lines on y axis.

Having this result, the last step draws the calculated lines on the source image and writes it to test_images_output
folder.


### 2. Potential shortcomings


One potential shortcoming is caused by the fact that algorithms used are dependent on the fixed values which are not 
changing depending on the image size and object distance from the camera.

One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Possible improvements

A possible improvement would be to ...

Another potential improvement could be to ...
