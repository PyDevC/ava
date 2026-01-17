---
id: hestreg
aliases: []
tags: []
---

# Hestreg

Hestreg is a hand gesture recognition system.

## SESSION

A Session is a window or windowless medium that starts the camera to run the program.

Division of session:

On the basis of window
- Window based 
- Windowless based

On the basis of Accelerator
- CPU based
- GPU based

Allow users to migrate from window session to windowless session and Vice Versa.

### Working

## ACCELERATOR

Only apply gpu accelerations that are useful for this project don't overdo it.

## MODEL MANAGER

Allow download and usage of pretrained models (don't give them option to train the model within the program if some wants a training medium? then there are much better solutions to that are available.)

## HAND SEGMENTATION and bg removal

Detect hand and remove the background keeping only image of hand to pass it as input in them model.

## GESTURE RECOGNITION

AI model to predict the hand gesture based on the number of input features passed

## CONTROL UNIT

OUTPUT of models can be then passed to the control unit to perform various actions on the device.

### Action list of the control unit

- Control mouse
- short message macro (keep a specific gesture as an alias)
- always ask before conforming (user can turn off this feature)

## REFERENCES
