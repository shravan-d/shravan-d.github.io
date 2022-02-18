---
layout: post
title: Image Segmentation and SegNet.
---
Localising and classifying different segments.

# What is image segmentation?
Image segmentation is a technique used to extract areas of interest in an image. This is most often done to reduce the complexity of image processing tasks by narrowing down the region of interest. An image is a collection of pixels; to achieve segmentation, we need to classify each pixel to a particular group. Thereupon, we can define image segmentation as an extension of image classification where we perform pixel-level localization. As shown in the image below, the segmented area is highlighted as the region of interest and rest is considered background.

A deeper look into segmentation would reveal two types, namely instance and semantic segmentation. 

## Instance Segmentation
Instance segmentation involves, as described above, grouping pixels of an object in an image together based on boundaries. In an image of a table with chairs, instance segmentation would identify each chair as a separate object, highlighting it's boundaries. 
## Semantic Segmentation
Semantic segmentation means to classify pixels into pre-defined classes. This requires training with images annotated with each of the classes. Considering the same example of an image of a table with chairs, semantic segmentation would classify asll the chairs as one object.

![Instance vs Sematic Segmentation]({{site.baseurl}}/media/images-2022-02-14-segmentation/instance.jpg
"Figure 1. Instance vs Sematic Segmentation by https://datascience.stackexchange.com/users/69822/blenz.")<br>

# Image Segmentation Techniques
I am going to touch upon the different techniques to perform segmentation before I delve deeper into one certain technique. Initial image processing techniques include using thresholds based on intensity, edge detection techniques based on Canny, Laplacian detectors, and watershed methods. And then, like for most other problem statements, deep learning provided a solution. 

With convolution neural networks being such a hit for image classification tasks, it comes as no surprise that CNNs are used for semantic segmentation. More specifically, a network that consisted of an encoder and a decoder was adopted. An encoder is a sequential connection of convolutional layers with activation and downsampling. The decoder, in contrast, has upsampling layers with convolution and acitvation. Therefore the input dimensions of the image match that of the output, thereby giving us a pixel-wise classification as we require. Let us look at the Segnet, one such model that performs semantic segmentation.

# SegNet
SegNet is a model proposed by Alex Kendall et al. It is an encoder-decoder CNN model that shows great results for semantic segmentation. The following is a demo by the authors of the original paper.

[![SegNet demo](https://img.youtube.com/vi/CxanE_W46ts/0.jpg)](https://www.youtube.com/watch?v=CxanE_W46ts)<br>

## Architecture
The Segnet has an encoder and a decoder network. They both have a similar convolutional structure but vary in tha fact that the encoder downsamples, while the decode upsamples. The structure follows a VGG-16 backbone with 13 layers. 
Encoder: 13 layers of convolution, batch normalisation, linear activation, and max pooling.
Decoder: 13 layers of convolution, batch normalisation, linear activation, and upsampling.
Finally, a K-class softmax layer is used to predict the segmentation masks.

![SegNet Architecture]({{site.baseurl}}/media/images-2022-02-14-segmentation/arc.png
"Figure 2. SegNet Architecture from the original paper")<br>

What makes SegNet unique and powerful is how the upsampling is carried out. Instead of randomly assigning indexes where the pixels values should go after upsampling, SegNet stores the indexes from the downsampling in the encoder. That means at each Pooling step in the encoder, the indexes of the maximum element is stored and is later used in the corresponding upsampling step in the decoder. This makes sure that the spatial features are maintained throughout the process, which otherwise would be lost when we downsample and then upsample. 

## SegNet Basic
To simplify the process, the authors of the original paper suggest a simplified model of SegNet that consists of 4 layers each in the encoder and decoder. The composition of the layers are the same, with the same pooling and upsampling techniques. I have implemented the architecture using Tensorflow and Keras. The basic architecture, although not as good as the original one in terms of performance does pretty well on the CamVid dataset. The CamVid dataset by Cambridge is a collection of 367 training images taken on the road, containing labels of 12 different classes ranging from vehicles to pedestrians. 

To train the model, RGB images are passed along with one-hot encoded masks that are basically pixel-level classification. Each channel in the label matrix represents one class and each pixel value is either 1 or 0, showing if the pixel is of the respective class or not. I retrieved only the two most commonly occuring classes in the dataset - vehicles and pedestrians, to carry out segmentation. Therefore my input labels consisted of 3 channels (background, vehicles, pedestrian), where for each element, one of the 3 channels was 1 and the other two were 0 as shown below.

![One Hot Encoding]({{site.baseurl}}/media/images-2022-02-14-segmentation/onehot.png
"Figure 3. One Hot Encoding")<br>

I have implemented the model with the pooling indices and corresponding upsampling and trained it with the CamVid dataset. The code is available in my Github repository about SegNet. The basic model gives about a 90% accuracy for the CamVid dataset with 3 classes. 

# Conclusion
The general encoder-decoder architecture is a good start for segmentation tasks. SegNet takes this one step further by implementing the storage and later usage of pool indices in downsampling and upsampling respectively. Another popular architecture is the U-net model which also comprises of an encoder-decoder architecture. U-net distinctly stores the entire feature maps (in contrast to just the indices in SegNet) after every step in the encoder, and concatenates these feature maps to the corresponding level in the decoder. This connection between the encoder and decoder layers is termed as 'skip connections' and is especially powerful when it comes to applications related to medical image analysis.

TODO - epochs image, results accuracy, result image, add github code, unet?

https://arxiv.org/abs/1511.00561