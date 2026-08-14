---
id: hand-gesture-recognition
aliases: []
tags: []
---

# Hand gesture recognition

A hand gesture recognition model is a program that detects the hand, figures out which gesture the hand is performing, and predicts that gesture. We may additionally decide to perform certain actions based on certain predictions. 

Main Idea of working:
- take in a stream of inputs of images
- output the classified image lable

- optional step: perform various tasks in a sequence to activate a commands.

Data: https://www.kaggle.com/datasets/gti-upm/leapgestrecog

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

The two-stage design exists for a reason: detecting a palm (a flat, low-poly pattern) is far more reliable and faster than detecting a full articulated hand. MediaPipe uses exactly this — palm detector first, then landmark regression on the crop. The landmarks are 21 2D/3D points (fingertips, joints, wrist) that describe hand pose compactly.

## Why landmarks instead of raw pixels

- Compress the input to ~21×2–3 numbers — tiny, fast, and robust to lighting/background (see [hestreg-model](hestreg-model.md) for the classification model that consumes them).
- The landmark representation is basically automated feature extraction for the gesture classifier (see [automated-feature-extraction](../automl/automated-feature-extraction.md)).

## Dynamic gestures (my project's focus)

Static gestures = one frame → one label. **Dynamic** gestures span multiple frames (wave, swipe), so the classifier gets a *sequence* of landmarks. Options:

- Feed consecutive frames to a small recurrent/Temporal CNN.
- Compute per-frame angles between landmarks and classify the pattern.
- Sliding window + majority vote to smooth predictions.

This is the multi-frame processing mentioned in [report](report.md).

## Practical tips

- Preprocess to reduce noise: blur, threshold, histogram equalization (the project list above).
- Real-time constraint → MobileNet-class backbones, keep resolution modest.
- Data: https://www.kaggle.com/datasets/gti-upm/leapgestrecog is a good starting point (see above).
