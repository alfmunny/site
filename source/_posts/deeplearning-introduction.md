---
title: Neural Networks and Deep Learning - 1. Introduction to Deep Learning
date: 2018-04-10 16:55:09
description: Notes of Coursera open course Netural Networks and Deep Learning. A short introduction of Neural Networks
categories:
- Computer
- Deep Learning
tags:
- python
- Coursera
photos:
---

These notes are taken from the Coursera open course [Netural Networks and Deep Learning](https://www.coursera.org/learn/neural-networks-deep-learning/home/welcome). Therefore here are only some important concepts that helped me to understand the materials. 

This note will introduce what is a neuron in Neural Networks.

## House Price

We can start with a simple example: predicting house price. 

Supposing we have a dataset of house price:

| x (size of the house) | y(price) |
| --------------------- | -------- |
| 30                    | 62       |
| 35                    | 83       |
| 58                    | 90       |
| 79                    | 124      |
| 92                    | 150      |
| 125                   | 188      |

{%asset_img image-20180410181806109.png "Predicting House Price" %}



The blue line in the plot is drawn to fit the points.

We can describe the blue line as a function in two parts: A linear function and a activation function.

According to the data, we can calculate the weight W and the bias b

Linear function: 
$$
f(x) = Wx + b = 2.3x - 46
$$
Activation function:
$$
f(z) = max(z, 0)
$$
Any input smaller than zero will output zero.

Combine these two:
$$
f(x) = max(Wx+b, 0)
$$
A neuron is just like this function. It accepts the input x, and give the output y. It uses the activation function to transfer the linear interpolation of the points to a non-linear interpolation. 

{% asset_img image-20180410194650766.png "neuron" %}

$f(z) = max(z, 0)$ is called **Rectified Linear Unit** (ReLU), which is used as activation function. There are some other activation funciton like sigmoid, tanh, leaky ReLu etc. They will be discussed in following articles.

Provided this single neuron model, we can predict the price at a given x, here the size of the house.fg

A Neural Network is to connect these neurons with diffrent input dimensons and parameters.

{% asset_img graph1.svg "Neural Network" %}

## Standard NN, CNN and RNN

Convolutional Neural Network can be used on image problems.

Recurrent Neural Network can be used on audio problems.

