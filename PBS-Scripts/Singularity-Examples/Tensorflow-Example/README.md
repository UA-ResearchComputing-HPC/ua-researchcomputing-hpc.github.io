# Tensorflow Example

## About
Script to run a tensorflow example on Ocelote using a tensorflow Singularity image on a Centos 7 GPU node. 

## PBS Script
[Click here to download example](singularity_tensorflow_example.tar.gz)

```
#!/bin/bash
#PBS -N tensorflow-example
#PBS -W group_list=<YOUR_GROUP>
#PBS -q standard
#PBS -j oe
#PBS -l select=1:ncpus=28:mem=224gb:np100s=1:os7=True
#PBS -l walltime=01:00:00

cd $PBS_O_WORKDIR
module load tensorflow
singularity exec --nv /cm/shared/uaapps/tensorflow/2.0/nvidia-tensorflow_2.0.0-py3.sif python3 tensorflow_example.py
```

## Tensorflow Script
```
#Linear Regression Example with TensorFlow v2 library 

from __future__ import absolute_import, division, print_function
#
import tensorflow as tf
import numpy as np
from tensorflow.keras import optimizers
tf.enable_eager_execution()
rng = np.random
#
# Parameters.
learning_rate = 0.01
training_steps = 1000
display_step = 50
#
# Training Data.
X = np.array([3.3,4.4,5.5,6.71,6.93,4.168,9.779,6.182,7.59,2.167,
              7.042,10.791,5.313,7.997,5.654,9.27,3.1])
Y = np.array([1.7,2.76,2.09,3.19,1.694,1.573,3.366,2.596,2.53,1.221,
              2.827,3.465,1.65,2.904,2.42,2.94,1.3])
n_samples = X.shape[0]
#
# Weight and Bias, initialized randomly.
W = tf.Variable(rng.randn(), name="weight")
b = tf.Variable(rng.randn(), name="bias")

# Linear regression (Wx + b).
def linear_regression(x):
    return W * x + b

# Mean square error.
def mean_square(y_pred, y_true):
    return tf.reduce_sum(tf.pow(y_pred-y_true, 2)) / (2 * n_samples)

# Stochastic Gradient Descent Optimizer.
optimizer = tf.keras.optimizers.SGD(learning_rate)
#
# Optimization process. 
def run_optimization():
# Wrap computation inside a GradientTape for automatic differentiation.
    with tf.GradientTape() as g:
        pred = linear_regression(X)
        loss = mean_square(pred, Y)

    # Compute gradients.
    gradients = g.gradient(loss, [W, b])    

    # Update W and b following gradients.
    optimizer.apply_gradients(zip(gradients, [W, b]))
#
# Run training for the given number of steps.
for step in range(1, training_steps + 1):
    # Run the optimization to update W and b values.
    run_optimization()    

    if step % display_step == 0:
        pred = linear_regression(X)
        loss = mean_square(pred, Y)
        print("step: %i, loss: %f, W: %f, b: %f" % (step, loss, W.numpy(), b.numpy()))
```
