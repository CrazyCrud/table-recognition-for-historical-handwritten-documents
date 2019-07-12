# Table recognition for historical handwritten documents


Based on the approach of [Liang, Xusheng](http://www.diva-portal.org/smash/record.jsf?pid=diva2:1292198) and [Lee, Benjamin](https://dcicblog.umd.edu/cas/wp-content/uploads/sites/13/2017/06/Lee.pdf) I tried to regonize handwritten tables. 

Liang's approach to extract lines based on connected components wasn't successfull. Instead Hough transformation needed to be applied.


## Run
```
docker-compose up -d
```
To receive the unique token to access Jupyter run:
```
docker logs --tail 3 jupyter
```

## Implementation

### Preprocessing

The contrast of the image is enhanced by using the [Contrast Limited Adaptive Histogram Equalization](https://docs.opencv.org/3.1.0/d5/daf/tutorial_py_histogram_equalization.html).

Then Gabor filter are used to extract the roughly horizontal and vertical foreground elements.

Next, Otsu binarization is conducted.

After the binarization process, resulted images are found to have a lot of noise elements. Thus, removing element based on element size is adopted (removing elements with width or height less or equal than one). Another approach would be the use of morphological opening which provided worse results.


### Line Detection


Liang took it a step further and studied ways to break down the connected components including text and line element. She adopted Hough line transformation and apply it on all Connected Components (CCs) to refine their shape before feature extraction. Before performing this operation, orientation and length of all the CCs wich are true table lines in training data are analyzed and their orientation range and the smallest length among them respectively in horizontal and vertical category are aquired. The orientation range for each CC is used as input parameter as well as *0.9xlength* of current CC is used as shortest detecting length in Hough line transform.

But in this case, the whole table is recognized as a connected component so the lines ca not be separeted.

In another attempt, I tried to recognize separate lines by their contour shape which didn't work out, too.

To recognize the lines Hough transformation is applied and the resulting lines are then filtered (Lee, 2017). 


### Cell Construction


The filtered lines are segmented into vertical and horizontal lines. Afterwards the intersection points are calculated and clustered by utilising the [MeanShift](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.MeanShift.html) algorithm.

The resulting clusters represent the corner points of each table cell.