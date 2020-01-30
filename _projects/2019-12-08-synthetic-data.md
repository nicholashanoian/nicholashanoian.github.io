---
layout: post
title: Differentially-private neural networks through synthetic data
description: ""
thumbnail: "/assets/images/projects/synthetic-data/epsilons.png"
mathjax: true
---

I recently finished a course on data privacy and we focused primarily
on using differential privacy as a mathematically rigorous way to
ensure that any individual's privacy is not violated. As a conclusion
to the course, we were to propose and complete a project that utilized
differential privacy. Most of the class focused on using _global_
differential privacy model, where individuals' raw data is stored on a
centralized server, and any analyst wishing to use this data must pass
differentially-private queries to the server and receives noisy
results. One potential problem with global differential privacy is
that there is a burden on the individual to trust the data holder not
to use the raw data. _Local_ differential privacy, on the other hand,
provides an additional security guarantee by never storing raw data
and adding noise to the data before it is stored within the database.
The tradeoff here is that the local model's accuracy is typically
orders of magnitude lower than the global model's for a given query.
While the accuracy is typically much worse, we still value the
importance of the security guarantee which the local model offers. We
set out to see how feasible it is to train a machine learning model by
using differentially-private data through the local model. We satisfy
the requirements of the local model by making each input example
differentially private before our model sees them at all.

## Dataset

We used the MNIST dataset which is comprised of images of handwritten
numbers and their labels, the true number values. We originally
planned on using both the images and their labels, but we quickly saw
the problem with using noisy labels. If the label is incorrect, the
entire example is driving the model in a completely wrong direction.
Insetad we decided to use an unsupervised clustering method which
ignores the labels entirely.

## Models

First, we attempted to train our own convolutional neural networks
which have been shown to be effective classifiers. After these results
were not at all promising, we looked at current literature to see if
any other methods would be more effective. We found a paper which
described and implemented a model called invariant informaiton
clustering which is a state-of-the-art method for unsupervised image
classification. This paper by Ji et al. even used the stanard version
of the MNIST dataset and achieved incredibly high accuracy of 99.2%
correct classification rate, so we reasoned that it should still be
able to achieve decent performacne if we add noise to the data.

To generate the noisy input data, we first took the 28x28 image and
normalized the pixel values so that the L2 norm of all the pixel
values is 1. We then added Gaussian noise using the Gaussian mechanism
to the data, with parameter values of sensitivity = 1, $\epsilon \in
\{150, 2000, 3000, 5000, 200000\}$, and $\delta = 1/(28 \cdot 28)^2$
(i.e., $1/n^2$ ). The model then builds a neural network classifier
from the noisy input data and the labels. The model works by taking an
input dataset and then applying an image transformations like rotation
and cropping- this is what makes the model invariant. At this point,
both datasets are run through an unsupervised CNN and then a fully
connected neural network to learn the natural clusters within the
data. Because these labels are being generated without knowing the
true labels, they are private. Results of this noising method for
various values of epsilon are shown below. Note that these are all
extremely high values of epsilon, and typically people consider values
of $\epsilon=10$ to be revealing a significant amount of data.

<p align="center"> 
<img src="/assets/images/projects/synthetic-data/epsilons-full.png" alt="Noised data with various epsilons">
</p>

## Results

As expected, the model trained on the raw data performs extremely well
and achieves a classification error of under 10 percent, with the loss
dropping down to below -2.5. However, as noise is added to the images,
the performance immediately and dramatically decreases. Initially,
with an epsilon of 150 which seemed reasonable (see noised examples
above) we can see that the loss bottoms out at -0.7 and the
classification error never drops below 0.89. This is the proportion of
examples in the plurality class which reflects that the model is
completely failing to learn the data.

To summarize, we see that with an epsilon of 150 and 2000, the model
is just picking up the plurality class. An epsilon of 3000 does
better; however, still not as good as the model trained on the raw
data. An epsilon value of 5000 surprisingly performs worse than an
epsilon value of 3000, and this could be just due to the random
initializations. An epsilon value of 200000 performs well; however,
this case effectively adds no noise to the data. We found the
magnitude of epsilon that we had to hit just to see improvements in
our results to be very surprising and disappointing.

The repository for this project which contains the full paper, code,
and slides can be found
[here](https://github.com/maxfieldEland/dp_nueral_nets)

![IIC results with various epsilons](/assets/images/projects/synthetic-data/iic-results.png)
