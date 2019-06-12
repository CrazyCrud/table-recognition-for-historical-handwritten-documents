# Table recognition for historical handwritten documents
Based on the approach of [Liang, Xusheng](http://www.diva-portal.org/smash/record.jsf?pid=diva2:1292198)

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

After the binarization process, resulted images are found to have a lot of noise elements. Thus, removing element based on element size is adopted (Removing elements with width or height less or equal than one). Another approach would be the use of morphological opening.

Liang took it a step further and studied ways to break down the connected components including text and line element. She adopted Hough line transformation and apply it on all Connected Components (CCs) to refine their shape before feature extraction.

...work in progress

### CC Production and Annotation
