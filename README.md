# CarND-Vehicle-Detection

In this project, the goal is to create a pipeline that can identify the the vehicles in the video.

The pipeline that I ended up with broken down into the following sections:
* Data Visualization
* HOG features
* Linear SVM

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
20.89 Seconds to train SVC with RGB...
acc for test set is:  0.973817567568

YUV:
5.26 Seconds to train SVC with YUV...
acc for test set is:  0.984797297297

YCrCb:
5.14 Seconds to train SVC with YCrCb...
acc for test set is:  0.993243243243

HSV:
5.8 Seconds to train SVC with HSV...
acc for test set is:  0.987612612613

LUV:
6.11 Seconds to train SVC with LUV...
acc for test set is:  0.989583333333

HLS:
6.65 Seconds to train SVC with HLS...
acc for test set is:  0.984797297297

pix_per_cell = 8, cell_per_block = 2, orient = 18
--------------------------------------------
RGB:
23.31 Seconds to train SVC with RGB...
acc for test set is:  0.97606981982

YUV:
11.71 Seconds to train SVC with YUV...
acc for test set is:  0.99268018018

YCrCb
9.94 Seconds to train SVC with YCrCb...
acc for test set is:  0.99268018018

HSV:
8.91 Seconds to train SVC with HSV...
acc for test set is:  0.990146396396

LUV:
8.81 Seconds to train SVC with LUV...
acc for test set is:  0.990709459459

HLS:
9.81 Seconds to train SVC with HLS...
acc for test set is:  0.988457207207

In looking at the accuracy, it seems like the feature/model to use is YCrCb with pix_per_cell = 8, cell_per_block = 2, orient = 9
