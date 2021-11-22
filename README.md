# Scuba Steve Rapid Image Annotation
Smart Annotation Platform with Deep Learning

This is a preview of a project produced by Chris Labbe, Anjan Patnaik and Art Jiang as our Capstone in the FourthBrain.AI  
Machine Learning Engineering eductional program.

## Overview
### Problem
Computer Vision Training needs lots of annotated images
Annotation requires manual outlining or point selection 
Global labelling market ~$1.3B in 2020 w/ est. CAGR of 25.6%

### Solution Hypothesis
Image object recognition technology today is amazing.  Computer systems can pick out and identify many different types of objects at very high accuracy of properly identifying what many of the items in an image are.

This technology has only been able to happen because of a huge set of images that have been labeled and segmented by humans to use as training sets for the machine learning models in use today.  One estimate says that it is a minimum of 300 samples of an object for the latest models to learn what that thing looks like and then start to be able to identify it in unlabeled images.

Our estimate of a human drawing an outline of an object in an image is 45 seconds.  Even if the person doing the labeling is much faster after a lot of practice, 30 seconds is likely a reasonable estimate for the outlining and processing of the image and relevant information.

Translating that to a famous dataset that has 27000 labeled images, each of which has many outlined and labeled objects, real people have been required to outline over 100,000 objects to make that dataset work properly.  Using the 30 second estimate suggests this is over 800 hours of non-stop work.  And this dataset only understands 150 generic object types.  For there to be a day where computers can process like humans and identification moves from “that is a bird” to “that is a mocking jay” will require many millions of labeled images at the cost of hundreds of thousands of labor hours.  In 2020 this market for labeling data was $1.3B, and it is expected to grow with a CAGR of 25%.

The goal of this project is to significantly reduce the effort required for this process through the use of generalized object identification partnered with human interaction.  The hypothesis is that a person simply performs a “click-drag” action to create a box to tell the system what part of the image to process and indicates through a drop-down list of options what the target object is.  If this works as expected, the time to find and create a training sample will be only a few seconds.  The resulting benefit in the image labeling world could be a reduction of cost or an acceleration of available training objects by a factor of 5 - 10x.

### Test of Value
Time to generate meaningful annotated images
Mean Average Precision (mAP) when compared with SOTA models

## Goals
* Improve annotation speed
* Easy to use, interactive and flexible interface 
* Reasonable quality contours in lowest time
* Train and infer on new classes of objects
* Improve SOTA Instance segmentation models

## Benefits


## What makes this project different?


## The App


## Tech Stack
### ML
Instance Segmentation - Pixellib
Class-less Semantic Segmentation - DeepMask
Utilize Deepmask to refine and improve instance segmentation classes detected.

### Deployments 
Dash Plotly based on Node.js, Flask. 
Microservices 
GCP Cloud Run 

## What's next?


