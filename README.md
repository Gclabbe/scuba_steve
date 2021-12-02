# Scuba Steve Rapid Image Annotation
Smart Annotation Platform with Deep Learning

This is a preview of a project produced by Chris Labbe, Anjan Patnaik and Art Jiang as our Capstone in the FourthBrain.AI Machine Learning Engineering eductional program.

## Overview
### Problem
Computer Vision Training needs lots of annotated images  
Annotation requires manual outlining or point selection which can be very slow and expensive
Global labelling market ~$1.3B in 2020 w/ est. CAGR of 25.6%

### Solution Hypothesis
Image object recognition technology today is amazing.  Computer systems can pick out and identify many different types of objects at very high accuracy of properly identifying what many of the items in an image are.

This technology has only been able to happen because of a huge set of images that have been labeled and segmented by humans to use as training sets for the machine learning models in use today.  One estimate says that it is a minimum of 300 samples of an object for the latest models to learn what that thing looks like and then start to be able to identify it in unlabeled images.

Our estimate of a human drawing an outline of an object in an image is 45 seconds.  Even if the person doing the labeling is much faster after a lot of practice, 30 seconds is likely a reasonable estimate for the outlining and processing of the image and relevant information.

Translating that to a famous dataset that has 27000 labeled images, each of which has many outlined and labeled objects, real people have been required to outline over 100,000 objects to make that dataset work properly.  Using the 30 second estimate suggests this is over 800 hours of non-stop work.  And this dataset only understands 150 generic object types.  For there to be a day where computers can process like humans and identification moves from “that is a bird” to “that is a mocking jay” will require many millions of labeled images at the cost of hundreds of thousands of labor hours.  In 2020 this market for labeling data was $1.3B, and it is expected to grow with a CAGR of 25%.

The goal of this project is to significantly reduce the effort required for this process through the use of generalized object identification partnered with human interaction.  The hypothesis is that a person simply performs a “click-drag” action to create a box to tell the system what part of the image to process and indicates through a drop-down list of options what the target object is.  If this works as expected, the time to find and create a training sample will be only a few seconds.  The resulting benefit in the image labeling world could be a reduction of cost or an acceleration of available training objects by a factor of 5 - 10x.

## Basic workflow
1)  Use Pixellib to infer segmentation on target image
2)  Prompt user to indicate what the target object is
3)  Prompt user to draw bounding box around target object 
4)  Use DeepMask to create mask of target object and display it
5)  User selects the best of 10 proposals for mask
6)  App saves the selected mask as contours ready for training

## Goals
* Improve annotation speed
* Easy to use, interactive and flexible interface 
* Reasonable quality contours in lowest time
* Train and infer on new classes of objects
* Improve SOTA Instance segmentation models

### Test of Value
* Time to generate meaningful annotated images
* Mean Average Precision (mAP) when compared with SOTA models

## Tech Stack
### The App
* Dash by Plotly
  * React.JS
  * Python
  * Flask
  * Produces interactive HTML dashboard
* CPU and GPU operational modes

### ML
* Instance Segmentation - Pixellib using Mask-RCNN on Tensorflow
* Class-less Semantic Segmentation - DeepMask on Pytorch
* Utilize Deepmask to refine and improve instance segmentation classes detected.

### Deployments
* Docker
* GCP Cloud and Cloud Run
* Desktop operation on any OS

### Data
* ADE20k
* COCO
* Pixellib Nature

## Training process
1) Extract images of 150 - 200 butterflies with original ground truth data and train a custom instance segmentation model on them for 100 epochs. Generally, we did not tune the hyperparameters and found an odd relationship between validation loss and our metric of choice AP@IOU, which will be discussed later.
2) Create DeepMask assisted ground truth data by using our tool to propose regions and not modifying those proposals at all on just the training set of data.  This data is extracted and converted into LabelMe format for training. Here we train the model again against the training set while leaving the validation or test set unchanged (in terms of which ground truth we are using). Our resulting evaluation of the model seeks to understand how well our tool generalizes to out
of sample hand labeled data.

## Outcomes as of November, 2021
### Metrics
During training our metrics of interest are training loss and validation loss. Both losses are the combination of multi-objective loss functions for each process. It is a weighted sum of different losses listed below:
* RPN Class Loss - loss assigned to the improper classification of anchor boxes
* RPN Bbox Loss - loss assigned to the localization accuracy of the region proposal network.
* MRCNN Class Loss - loss corresponding to improper classification of the object in the region proposal.
* MRCNN BBox Loss - loss corresponding to localization of bounding box on identified class
* MRCNN Mask Loss - loss corresponding to mask on a pixel level on identified objects

Our outcome metric of interest is Mean Average Precision @ IOU. This metric essentially takes the mean area under the curve of the precision and recall line across all classes and can be valued at different Intersection over Union thresholds.

Breaking this down further we can define a few terms by starting with key concepts.
* True Positives (TP) = # of times the mask adequately overlaps with the ground truth and is the right class.
* False Positive (FP) = # of times the mask predicts a class that isn’t there.
* False Negative (FN) = # of times the mask misses a class that IS there.

There is also another parameter that can vary. In our definition of TP, FP and FN we have to take into account not only the class prediction but also how well our predicted  
mask overlaps with the ground truth mask. This metric is called Intersection over Union and affects the Average Precision measurement at different thresholds.

Picture goes here

So when we say Average Precision @ IOU = 0.5 this means we are looking for Average Precision when the ground truth and mask overlap is at least 50% of their union. Any
overlap that is below our threshold will be considered an FN. If we get stricter about what IOU threshold we use, Average Precision will naturally decline.

Training result graph goes here

Results from training on butterflies data with original training labeled data and augmented training label data (using DeepMask) are shown above.

We are able to generally replicate the pixellib baseline using the original labeled data.  Even when we use the DeepMask labeled data we get comparable results at IOU  
thresholds up to 0.7. However there is a steep decline at higher IOU thresholds. This makes sense because our labeled data might not always approximate boundaries well  
which penalizes us at stricter IOU requirements. This can potentially be remedied by tuning the weights of our loss function to weight the mask loss higher and therefore  
ensure pixel precise boundaries at training time (this can give us a better approximation on how noisy we are making the ground truth labels with our approach).  

### Time
One of the other key areas we were evaluating was the actual time saved by utilizing DeepMask assisted annotation. By timing how long it takes to manually annotate images (45 second on average) we were able to see a 4.5x gain in annotation time. The implication for this is that we can generate 4.5x labels in the same amount of time.  

The natural extension of this finding is to see how the imprecision in ML assisted labelling trades off with the increased number of images we have. Specifically we’d  
want to know how many imprecisely (but quickly) labeled images it takes to converge in performance to more precisely (but slow) labeled images. If the ratio between the two is  
less than 4.5x we have a winning candidate.

## Conclusion
The current results from the initial training run show tremendous potential.  The resulting evaluation on the test data produces a mean average precision curve that is fairly comparable to the models trained with hand-annotated images.  And the work to create these annotation took 5x less time to produce (on CPU).

There is a list of improvements that will close the gap further:
* Advance to SharpMask over DeepMask
* DeepMask operation on GPU instead of CPU
* Build all masking options at once instead of request
* And more ...

There is a more detailed write-up in the docs section of this repo.

If you'd like to get involved or learn more about this project please contact chris-scubasteve @ sunshinelobby.com

## References
References
Chen, T., Lin, L., Wu, X., Xiao, N., & Luo, X. (2018). Learning to Segment Object
Candidates via Recursive Neural Networks. IEEE Transactions on Image Processing, 27(12),
5827–5839. https://doi.org/10.1109/tip.2018.2859025

Ayoola Olafenwa. (2020). GitHub - ayoolaolafenwa/PixelLib: PixelLib,
https://pixellib.readthedocs.io/en/latest/. GitHub. https://github.com/ayoolaolafenwa/PixelLib

@inproceedings{DeepMask,
title = {Learning to Segment Object Candidates},
author = {Pedro O. Pinheiro and Ronan Collobert and Piotr Dollár},
booktitle = {NIPS},
year = {2015}
}

@inproceedings{SharpMask,
title = {Learning to Refine Object Segments},
author = {Pedro O. Pinheiro and Tsung-Yi Lin and Ronan Collobert and Piotr Dollár},
booktitle = {ECCV},
year = {2016}
