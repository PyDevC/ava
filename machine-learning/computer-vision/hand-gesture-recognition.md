---
id: hand-gesture-recognition
aliases: []
tags: []
---

A hand gesture recognition model is a program that detects the hand, figures out which gesture the hand is performing, and predicts that gesture. We may additionally decide to perform certain actions based on certain predictions. 

Main Idea of working:
- take in a stream of inputs of images
- output the classified image lable

- optional step: perform various tasks in a sequence to activate a commands.

get dataset for training the hand gesture recognition model: https://www.kaggle.com/datasets/gti-upm/leapgestrecog

## Steps
- Detect hand in the image
- if there is hand in the image then detect hand landmark points.
- preprocess the image to reduce noise
- train a model on hand gesture data
- train and deploy the model
- use the trained model to predict the gesture performed in the video
- perform various tasks using a combination of commnads

## hand landmark detection

The hand landmarker model bundle contains a palm detection model and a hand landmarks detection model. The Palm detection model locates hands within the input image, and the hand landmarks detection model identifies specific hand landmarks on the cropped hand image defined by the palm detection model.
