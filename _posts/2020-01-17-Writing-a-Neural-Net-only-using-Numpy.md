---
layout: post
title: Writing a Neural Net only using Numpy
cover: prog1.png
date:   2020-01-17 19:20:00
categories: posts
---
## Introduction

This project is a very simple take on Machine Learning. If you're a beginner on the topic and you want to code something that works in 30 minutes, this is for you. I wrote this script a few months ago just to get myself into the world of Machine Learning. The goal was to create a program that can recognize hand written digits after being trained on a sample set. I'm using scikit-image's imsave function and the codec module to handle the input images, the Neural Net itself is written in pure Numpy. You can find the repository [here](https://github.com/RoubenRehman/HandWrittenDigitsNeuralNet).

## The MNIST Database

The dataset used in this project is the _MNIST Database of handwritten digits_ by Yann LeCun, Corinna Cortes and Christopher J.C Burges. It consists of 70.000 28x28 pixel large greyscale images, 60.000 of which are intended for training purposes. The remaining 10.000 are used to test the code.

The database comes in a non-standardized, yet very simple format called IDX. In essence, the file starts with a magic number, followed by the definition of all necessary dimensions. This would be number of images, width and height for an image database and number of labels for a label database. All these values are 32bit integers. What follows is the raw data (pixel value or label), each value is of type unsigned int. To read the databases and output the images as png if desired, I used [Gosh4AI's code](https://www.youtube.com/watch?v=6xar6bxD80g):

```python
datapath = './resources/'
files = os.listdir(datapath)
```  
<br>
This opens up the resources folder containing all the MNIST databases. Now we are for-looping for each file in files, to consecutively read all four databases. For each file, we are first checking the magic number. If it's 2051, the database contains image data so we read and resize the pixel values as follows:


```python
if type == 2051:    # magic number indicates images
    category = 'images'
    num_rows = get_int(data[8:12])  # getting height of image in pixels
    num_cols = get_int(data[12:16]) # getting width of image in pixels
    parsed = np.frombuffer(data, dtype = np.uint8, offset = 16) # reading all following data
    parsed = parsed.reshape(length, num_rows, num_cols) # resizing the data accordingly
```  
<br>
If the magic number is equal to 2049, we are reading a label database:

```python
elif type == 2049:  # magic number indicates labels
    category = 'labels'
    parsed = np.frombuffer(data, dtype = np.uint8, offset = 8)
    parsed = parsed.reshape(length)
```  
<br>
Now we check the length of the database to determine whether it's a training set or a test set and afterwards save the np-array in a dictionary:

```python
if length == 10000:     # testing set
    set = 'test'

elif length == 60000:   # training set
    set = 'train'

data_dict[set+'_'+category] = parsed
```  
<br>
Further information and all databases can be found on [LeCun's website](http://yann.lecun.com/exdb/mnist/). The databases are also included in this project's repository.

## The Math

The algorithm I used here is pretty simple and easy to understand. I didn't use fancy techniques like convolution and didn't preprocess the images in any way. The Net has one input-layer with 784 neurons, each corresponding to one pixel of the image. Inside, there are two hidden layers with 16 neurons each and the output layer has 10 neurons. The idea is, that the index of the highest value in the output layer is the net's guess and the actual value is the percentage of it's certainty. So if the output layer would be

```
o = [0, 0.003, 0.01, 0, 1.22, 1.02, 0, 97.2, 0.02, 0.527]
```
<br>
then the Net's guess would be 7 with a certainty of 97.2 percent.  

I used the classic sigmoid function as activation function just because it's the easiest to deal with. I didn't use any biases. The sigmoid function and it's derivative are defined within the code as follows:

```python
# Activation function
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# Activation function derivative
def sigmoid_derivative(x):
    return x * (1 - x)
```
<br>
To actually train the Net, we iterate over every image saved in the dictionary that is labeled "train". First, the input has to be fed forward through the Net:

```python
for it in range(60000):
    # feed forward
    l0 = data_dict['train_images'][it].reshape((1, 784))
    l1 = sigmoid(np.dot(l0, syn0))
    l2 = sigmoid(np.dot(l1, syn1))
    l3 = sigmoid(np.dot(l2, syn2))
```
<br>
Then back propagation starts and we calculate the overall error and each layer's delta:

```python
    # back propagation
    expt_out = np.zeros((1, 10))
    expt_out[0][data_dict['train_labels'][it]] = 1
    l3_err = expt_out - l3

    # calculating deltas
    l3_delta = l3_err*sigmoid_derivative(l3)

    l2_err = l3_delta.dot(syn2.T)
    l2_delta = l2_err*sigmoid_derivative(l2)

    l1_err = l2_delta.dot(syn1.T)
    l1_delta = l1_err*sigmoid_derivative(l1)
```
<br>
After that, we just have to update all connections as follows:

```python
    # updating
    syn2 += l2.T.dot(l3_delta)
    syn1 += l1.T.dot(l2_delta)
    syn0 += l0.T.dot(l1_delta)
```
<br>
What we're trying to do here is formulating a measure on "how wrong" the Net's guess is, the so-called _cost function_, and then minimizing that function through gradient descent. In this example, the cost function is just the Net's raw error:

```python
l3_err = expt_out - l3
```
<br>
By changing the Net's connection values in such a way, that we minimize this function, we in turn "train" the Net to recognize the digits correctly.
To fully understand this algorithm, I'd recommend [3blue1brown's video series](https://www.youtube.com/watch?v=aircAruvnKk&list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) on Neural Nets. [Towards Datascience](https://towardsdatascience.com/) also has a lot of good explanations on algorithms, terminology and more.

## Summing up

There's not much to say really, feel free to mess around with the code however you want, especially if you're a beginner. Try to change up the architecture of the Net or plug in a different activation function. There's a lot of documentary and tutorials out there so you should have no problem getting yourself kick-started in the world of Machine Learning.
