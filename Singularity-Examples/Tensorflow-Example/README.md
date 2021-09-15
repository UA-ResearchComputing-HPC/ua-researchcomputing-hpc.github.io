# Tensorflow Example

## About 
This script makes use of a Tensorflow Singularity image available as a module on HPC to execute a workflow on a GPU node. 

As a note: When loaded in an interactive session, you can easily execute the Tensorflow image using the alias ```tensorflow```. However, because aliases don't carry over into batch environments, you will need to provide the full ```singularity exec``` command to run your workflow. If you ever need to track down exactly what that command is, you can log into an interactive session and check, e.g.:
```
[netid@r1u04n2 ~]$ module load tensorflow/nvidia/2.0.0 
[netid@r1u04n2 ~]$ type tensorflow
tensorflow is aliased to `singularity exec --nv /contrib/singularity/nvidia/nvidia-tensorflow_2.0.0-py3.sif python3'
```

## Submission Script
[Click here to download example](Tensorflow-Example.tar.gz)
```
#!/bin/bash
#SBATCH --output=Sample-tensorflow-example-%a.out
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --gres=gpu:1
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load tensorflow/nvidia/2.0.0 
singularity exec --nv /contrib/singularity/nvidia/nvidia-tensorflow_2.0.0-py3.sif python3 tensorflow_example.py
```

## Example Tensorflow Script
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

## Job Submission
```
[netid@wentletrap ~]$ sbatch tensorflow_example.slurm 
Submitted batch job 2069594
```

## Output
```
[netid@junonia ~]$ tail -n 22 Sample-tensorflow-example-4294967294.out 
2021-09-15 12:55:19.106307: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1304] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 30173 MB memory) -> physical GPU (device: 0, name: Tesla V100S-PCIE-32GB, pci bus id: 0000:81:00.0, compute capability: 7.0)
step: 50, loss: 0.101393, W: 0.340310, b: 0.170136
step: 100, loss: 0.098595, W: 0.335085, b: 0.207178
step: 150, loss: 0.096117, W: 0.330168, b: 0.242037
step: 200, loss: 0.093922, W: 0.325541, b: 0.274842
step: 250, loss: 0.091979, W: 0.321186, b: 0.305714
step: 300, loss: 0.090257, W: 0.317088, b: 0.334767
step: 350, loss: 0.088733, W: 0.313232, b: 0.362109
step: 400, loss: 0.087383, W: 0.309602, b: 0.387839
step: 450, loss: 0.086187, W: 0.306187, b: 0.412054
step: 500, loss: 0.085128, W: 0.302972, b: 0.434841
step: 550, loss: 0.084191, W: 0.299948, b: 0.456286
step: 600, loss: 0.083360, W: 0.297101, b: 0.476467
step: 650, loss: 0.082625, W: 0.294422, b: 0.495460
step: 700, loss: 0.081973, W: 0.291901, b: 0.513333
step: 750, loss: 0.081396, W: 0.289528, b: 0.530153
step: 800, loss: 0.080885, W: 0.287296, b: 0.545982
step: 850, loss: 0.080433, W: 0.285195, b: 0.560878
step: 900, loss: 0.080032, W: 0.283217, b: 0.574897
step: 950, loss: 0.079677, W: 0.281356, b: 0.588090
step: 1000, loss: 0.079363, W: 0.279605, b: 0.600505
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=73&local_job_id=2069594 by 8am on 2021/09/16.
```

*****
[back](../)
