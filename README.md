# CarND-Vehicle-Detection

In this project, the goal is to create a pipeline that can identify the the vehicles in the video.

The pipeline that I ended up with can be broken down into the following sections:
* Data Visualization
* HOG features
* Linear SVM
* Video Pipeline

Data Visualization
--------------------------------------------
When working with a set of data, it is always important to get a sense of what the data set contain. In this case, the dataset contains 8792 images of vehicles and 8968 images of non vehicles. In the first glance, I think there should be a larger set of data for non vehicles, since within any frame, the majority of the frame will be non vehicles. Here are some of the examples from the dataset.

<img width="1010" alt="screen shot 2017-03-14 at 8 15 16 pm" src="https://cloud.githubusercontent.com/assets/22971963/23932050/fed5b5a0-08f2-11e7-8be4-4f7d37a9eaac.png">

Now that I know what I am working with, it is time to see how to separate a vehicle against non vehicle.

HOG Features
--------------------------------------------
The first to do is to identify the unique features of what is separating between a vehicle and non-vehicle. There are many ways to do that, which could be the difference in colors, in shapes and in gradient. In this particular case, I decided to use the HOG features, which stands for Histogram of Oriented Gradient. Basically, the HOG is separating the image into small cells, then return the overall gradient orientation of the pixel within the cell. This gives a unique signature of an image which is robust to some variable in shape and size.Here are some example of the HOG features.

<img width="997" alt="screen shot 2017-03-14 at 8 40 22 pm" src="https://cloud.githubusercontent.com/assets/22971963/23932643/8ec5d098-08f6-11e7-93fe-7426f74870ea.png">
<img width="995" alt="screen shot 2017-03-14 at 8 40 04 pm" src="https://cloud.githubusercontent.com/assets/22971963/23932644/8ec698f2-08f6-11e7-9bf2-473d148fa6d3.png">
<img width="998" alt="screen shot 2017-03-14 at 8 39 59 pm" src="https://cloud.githubusercontent.com/assets/22971963/23932642/8ec4da8a-08f6-11e7-8d4f-c68e4b218e81.png">

From the images, we can see that the HOG features show a difference between vehicle and non-vehicle and I can see a general shape of a car.

Linear SVM
--------------------------------------------
Now that I know which feature to use, I can begin training my model. The model that is used here is the Linear SVM, this is the best in term of the combination between speed and accuracy. Here is the result of the model performance in using different orientation and color.

pix_per_cell = 8, cell_per_block = 2, orient = 9
--------------------------------------------
RGB: 
15.26 Seconds to train SVC with RGB...
acc for test set is:  0.970439189189

YUV:
4.36 Seconds to train SVC with YUV...
acc for test set is:  0.990990990991

YCrCb:
5.02 Seconds to train SVC with YCrCb...
acc for test set is:  0.991835585586

HSV:
6.02 Seconds to train SVC with HSV...
acc for test set is:  0.987331081081

LUV:
4.78 Seconds to train SVC with LUV...
acc for test set is:  0.987331081081

HLS:
6.03 Seconds to train SVC with HLS...
acc for test set is:  0.986768018018

pix_per_cell = 8, cell_per_block = 2, orient = 18
--------------------------------------------
RGB:
24.82 Seconds to train SVC with RGB...
acc for test set is:  0.973536036036

YUV:
7.88 Seconds to train SVC with YUV...
acc for test set is:  0.990990990991

YCrCb
9.48 Seconds to train SVC with YCrCb...
acc for test set is:  0.991554054054

HSV:
13.29 Seconds to train SVC with HSV...
acc for test set is:  0.990146396396

LUV:
8.55 Seconds to train SVC with LUV...
acc for test set is:  0.990990990991

HLS:
9.6 Seconds to train SVC with HLS...
acc for test set is:  0.986768018018

In looking at the accuracy and the time to train the model, it seems like the feature/model to use is YCrCb with pix_per_cell = 8, cell_per_block = 2, orient = 9 give the best result. This will be the classifer that I will be using for the project.


Sliding Window method
--------------------------------------------
Now that I have a classifer, I will need to be able to prediction where the vehicle is in the image. The technique that is used is called the sliding window. Basically, a window of a specific size will travel through the image, and each time it moves, we will use the model to classify if there is a car there or not. Below is a example of all the overlapping windows that this technique will use.

<img width="530" alt="screen shot 2017-03-14 at 8 59 46 pm" src="https://cloud.githubusercontent.com/assets/22971963/23933069/33ebb298-08f9-11e7-9a15-71ddcb3b5e12.png">

Then, using the combination of HOG features and the YCrCb colorspace, this is where the classifier think the car is located.

<img width="532" alt="screen shot 2017-03-14 at 9 03 12 pm" src="https://cloud.githubusercontent.com/assets/22971963/23933126/a9f4b6b0-08f9-11e7-8b4f-9ca6cf18c9a9.png">

and this is a pretty good estimation! However, realistically, we only want one bounding box for each of the car and therefore, more work has to be done.

Heatmap method
--------------------------------------------
The next step to find one bounding box for each car and the technique to use is the heatmap method. The way this work is that for every pixel that is bounded with in the box, we will add 1 to the pixel. Here is the result image.

<img width="523" alt="screen shot 2017-03-14 at 9 06 24 pm" src="https://cloud.githubusercontent.com/assets/22971963/23933245/62d56b98-08fa-11e7-8cb9-a8fd4bb8e967.png">

and again, we can see that there are 2 blob in the image. However, now we can use that information and determine the max and min position of the individual blob. This max and min position is the bounding box of the car.

<img width="531" alt="screen shot 2017-03-14 at 9 10 02 pm" src="https://cloud.githubusercontent.com/assets/22971963/23933296/c1aa8d10-08fa-11e7-95fa-bee278d81135.png">

We can now see 2 individual box that is bounding the car. Even though this is not perfect but it does give a good estimation of where the car is located.

With this we now have a pipeline to do car detection. However, this pipeline will be really slow because for every single image, the pipeline needs to go through 736 windows to extract features and make a prediction. 

There are a few ways to help with this problem. First we can reduce the window numbers so that the pipeline doesn't need to extract features for 736 times and also we can use a technique called HOG subsampling. This technique basically only require the pipeline to take the HOG feature once for each image, and it is shown to be a big time saver.

In many cases, there will be some false positive, this is where the model will return that there is a car when there are only lane markings. Here is an example below.

<img width="473" alt="screen shot 2017-03-19 at 12 54 10 am" src="https://cloud.githubusercontent.com/assets/22971963/24079141/9e1d3518-0c3e-11e7-9c63-e12e3ef48f5f.png">

<img width="470" alt="screen shot 2017-03-19 at 12 55 45 am" src="https://cloud.githubusercontent.com/assets/22971963/24079157/d63e99b4-0c3e-11e7-8cc5-a028aa6f2c22.png">

It can be seen that, there is a bounding box in there image even though there is no car in the image. TO help solve this problem, this is where the heatmap can be a useful tool. Since the heatmap can show the intensity for the pixel, we can basically set a threshold to the heatmap and eliminate all the pixel that is under a specific value to be zero. This way, we can eliminate the area where we have have no confidence that it is a car (false positive) while keeping the area where we have a high confidence that it is a car.

By setting the threshold to 3, we can see that the false positive is no longer an issue in that image.

<img width="463" alt="screen shot 2017-03-19 at 1 02 51 am" src="https://cloud.githubusercontent.com/assets/22971963/24079210/e5271a54-0c3f-11e7-807f-f1a1c1f821e0.png">

Video Pipeline
--------------------------------------------
Now that I have all the tools needed to identify the potential position of the car (sliding windows) and a way to eliminate the false positive. I can now create a pipeline to detect vehicles in the video stream. 

Here some of the features/approach that I took:

First, running a full scan of the image and use the HOG subsampling technique to find the bounding box of the where the car is. Once I do that, I can use that information to narrow down the search area when I search in the next frame. Instead of a full scan on the whole image, the next scan will be focused on where the bounding box was found in the previous frame. There are two benefits that came with this methods. 

1) With a narrower search, the chances of getting false positive will decrease
2) Lower runtime (since runtime is lower, the overlapping for this type of search is 1 pix per step instead of 2 or 4 pix per step)

Then, after every 8 frames of subimage scan, a full image scan will be performed in case if any new cars are coming into the image. 

During these 8 frames, the resulting heatmap will be recorded and summed together. Basically, adding the heatmap together will give up a higher confidence in where the car is located. By setting another threshold, false negative can be again eliminated.

If in any case, where we detected a car for a one frame and lose it for the next, I will keep the bounding box the same as the previous bound just in case that this is a misdetection due to lighting change or a poor model output. 

Reflection
--------------------------------------------
There are still many things that I would like to improve in this pipeline. 

For instance, sometime, depending on the threshold that I set for the heatmap, the heatmap will return 2 separate blobs even though both blobs should be the same car. I would like to implemente a way to dilate the image where it will connect blobs that are close enough to each other. 

Additionally, I would like to find the centroid of the bounding box and record the movement from frame to frame, this way, I can tell how fast and also the position that the other cars is going in respect of me.

This is a really great and challenging project that allow us to truly understand how tough the detection problem is. Although this is one of the older technique, but it definitely allowed me to put everything into prespective. This made me want to study more on how the state-of-the-art technique works (such as YOLO).

Here is the result using the pipeline for the project video:
https://youtu.be/I9tk11dycCw








