# **Behavioral Cloning** 

**Behavioral Cloning Project**

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./writeup-images/CNN.png "NVidia CNN"
[image2]: ./writeup-images/center.jpg "Center Image"
[image3]: ./writeup-images/left.jpg "Left Image"
[image4]: ./writeup-images/right.jpg "Right Image"
[image5]: ./writeup-images/center_gray.jpg "Gray Image"
[image6]: ./writeup-images/center_res.jpg "LowRes Image"
[image7]: ./writeup-images/center_crop.jpg "Crop Image"


---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* writeup_report.md summarizing the results

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

#### 3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

My model consists of the following layers:  
![alt text][image1]

This model was obtained from NVidia's paper "End to End Learning for Self-Driving Cars":

The model includes RELU activation after each convolution to introduce nonlinearity (code line 80-89), the data is cropped and normalized in the model using Keras cropping and lambda layers (code lines 77-78). 

#### 2. Attempts to reduce overfitting in the model

Considering that the model achieved a good result using only 1 epoch of training; that it was trained and validated on different data sets; and the results obtained met the minimum criteria, no layer to reduce overfitting was necessary.

Epoch 1/1  
10556/10556 [==============================] - 4329s 410ms/step - loss: 0.0043 - val_loss: 0.0075  

The model was tested by running it through the simulator and ensuring that the vehicle could stay on the track.

#### 3. Model parameter tuning\
The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 99).

#### 4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of data provided by Udacity and 3 more laps generated by me. One of them was generated by driving the course backwards to reduce bias for left turns.

For details about how I created the training data, see the next section. 

### Model Architecture and Training Strategy

#### 1. Solution Design Approach

Considering the lack of GPU to process lots of data, my approach was to follow all the tips from "Behavioral Cloning Cheatsheet"

My first step was to use a convolution neural network model similar to the NVidia's paper "End to End Learning for Self-Driving Cars". I thought this model might be appropriate because it was used on a very similar task for real world cars, so I considered that that would be the best first attempt.

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set and ran it for only one epoch. My idea was to run the training one epoch at a time, and save the model after each one. Doing so, I would be able to find how many epochs were necessary to complete the task without overfitting and also I would be able to use transfer learning to fine tune my model if it was necessary.

Then after the first epoch, that took aprox. 1 hour to train, I got the following results:

Epoch 1/1  
10556/10556 [==============================] - 4329s 410ms/step - loss: 0.0043 - val_loss: 0.0075  

The final step was to run the simulator to see how well the car was driving around track one. For my surprise, the model did the first run without ever leaving the road.

So, with an already working model, I tried to train it for one more epoch, to see if the behavior was improved, but the model overfitted. 

Epoch 1/1  
10556/10556 [==============================] - 4254s 403ms/step - loss: 6.2039e-04 - val_loss: 0.0071

So I kept the first model solution.

#### 2. Final Model Architecture

The final model architecture (model.py lines 75-94) consisted of a convolution neural network with the following layers and layer sizes:

| Layer         		|     Output Shape	        					| Param \# |
|:---------------------:|:------------------------------:|:---------------:|
|Cropping2D     |   (None, 43, 160, 3)      |  0         |
|Lambda         |   (None, 43, 160, 3)      |  0         |
|Conv2D         |   (None, 22, 80, 24)      |  1824      |
|Conv2D         |   (None, 11, 40, 36)      |  21636     |
|Conv2D         |   (None, 6, 20, 48)       |  43248     |
|Conv2D         |   (None, 4, 18, 64)       |  27712     |
|Conv2D         |   (None, 2, 16, 64)       |  36928     |
|Flatten        |   (None, 2048)            |  0         |
|Dense          |   (None, 100)             |  204900    |
|Dense          |   (None, 50)              |  5050      |
|Dense          |   (None, 10)              |  510       |
|Dense          |   (None, 1)               |  11        |


Total params: 341,819  
Trainable params: 341,819  
Non-trainable params: 0  
_________________________________________________________________  

#### 3. Creation of the Training Set & Training Process

To capture good driving behavior, I first recorded two laps on track one using center lane driving plus one lap driving the track backwards. Then I added Udacity's dataset to my own and I got almost 14k samples. Here is an example image of center lane driving:

![alt text][image2]

To augment the dataset and enable the vehicle to recover to the center of the lane, I used the left and right camera images adding a correction on the steering angle of 0.2 (model.py lines 50, 60). Doing so I tripled the number of samples.  
Here are some examples of left and right camera images:

![alt text][image3]
![alt text][image4]

After the collection process, I had almost 40k data points. I then preprocessed this data by first changing to grayscale (model.py line 38, 48, 58):

![alt text][image5]

Then I resized the image to have half of its original resolution (according to "Behavioral Cloning Cheatsheet" this was a good step to perform to reduce processing time) (model.py lines 39, 49 ,59). This step also made me change drive.py to resize the images that were to be predicted (drive.py line 65):

![alt text][image6]

Inside the model I performed cropping and normalization of data (model.py lines 77-78):

![alt text][image7]

I finally randomly shuffled the data set (model.py lines 24, 66) and put 20% of the data into a validation set. (model.py line 18)

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. The ideal number of epochs was 1 as evidenced by the vehicle completing the track after the first epoch and overfitting after the second.  
I used an adam optimizer so that manually training the learning rate wasn't necessary.
