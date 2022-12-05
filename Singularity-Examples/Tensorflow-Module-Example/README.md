# Tensorflow Module Job Example

[![](/Images/Download-Button.png)](tensorflow-module-job.tar.gz)

## About 
This script makes use of a Tensorflow Singularity image available as a module on HPC to execute a workflow on a GPU node. 

As a note: When loaded in an interactive session, you can easily execute the Tensorflow image using the alias ```tensorflow```. However, because aliases don't carry over into batch environments, you will need to provide the full ```singularity exec``` command to run your workflow. If you ever need to track down exactly what that command is, you can log into an interactive session and check, e.g.:
```console
[netid@r1u04n2 ~]$ module load tensorflow/nvidia/2.0.0 
[netid@r1u04n2 ~]$ type tensorflow
tensorflow is aliased to `singularity exec --nv /contrib/singularity/nvidia/nvidia-tensorflow_2.0.0-py3.sif python3'
```

## Submission Script

```bash
#!/bin/bash
#SBATCH --output=Sample-tensorflow-example-%A.out
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
```python
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
```console
[netid@wentletrap ~]$ sbatch tensorflow_example.slurm 
Submitted batch job 1471449
```

## Output
```console
[netid@wentletrap ~]$ cat Sample-tensorflow-example-1471449.out 
Tensorflow executable:  /opt/ohpc/pub/apps/tensorflow/nvidia/2.0.0/tensorflow
The command tensorflow is a bash script added to your PATH. Contents of the executable: 

#!/bin/bash
singularity exec --nv /contrib/singularity/nvidia/nvidia-tensorflow_2.0.0-py3.sif python3 $@

Executing: tensorflow tensorflow-example.py

INFO:    underlay of /etc/localtime required more than 50 (90) bind mounts
INFO:    underlay of /usr/bin/nvidia-smi required more than 50 (372) bind mounts
Usage example: change_mofed_version.sh 4.5-1.0.1
2022-12-05 15:20:32.584905: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudart.so.10.2
WARNING:tensorflow:From tensorflow-example.py:8: The name tf.enable_eager_execution is deprecated. Please use tf.compat.v1.enable_eager_execution instead.

2022-12-05 15:20:35.118859: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcuda.so.1
2022-12-05 15:20:35.146988: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1618] Found device 0 with properties: 
name: Tesla P100-PCIE-16GB major: 6 minor: 0 memoryClockRate(GHz): 1.3285
pciBusID: 0000:0b:00.0
2022-12-05 15:20:35.147020: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudart.so.10.2
2022-12-05 15:20:35.189363: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcublas.so.10
2022-12-05 15:20:35.208855: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcufft.so.10
2022-12-05 15:20:35.216036: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcurand.so.10
2022-12-05 15:20:35.261024: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusolver.so.10
2022-12-05 15:20:35.268836: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusparse.so.10
2022-12-05 15:20:35.344562: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudnn.so.7
2022-12-05 15:20:35.344988: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1746] Adding visible gpu devices: 0
2022-12-05 15:20:35.379485: I tensorflow/core/platform/profile_utils/cpu_utils.cc:94] CPU Frequency: 2399855000 Hz
2022-12-05 15:20:35.379611: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x51a8ea0 initialized for platform Host (this does not guarantee that XLA will be used). Devices:
2022-12-05 15:20:35.379623: I tensorflow/compiler/xla/service/service.cc:176]   StreamExecutor device (0): Host, Default Version
2022-12-05 15:20:35.465586: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x520f7e0 initialized for platform CUDA (this does not guarantee that XLA will be used). Devices:
2022-12-05 15:20:35.465617: I tensorflow/compiler/xla/service/service.cc:176]   StreamExecutor device (0): Tesla P100-PCIE-16GB, Compute Capability 6.0
2022-12-05 15:20:35.465895: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1618] Found device 0 with properties: 
name: Tesla P100-PCIE-16GB major: 6 minor: 0 memoryClockRate(GHz): 1.3285
pciBusID: 0000:0b:00.0
2022-12-05 15:20:35.465923: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudart.so.10.2
2022-12-05 15:20:35.465946: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcublas.so.10
2022-12-05 15:20:35.465958: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcufft.so.10
2022-12-05 15:20:35.465968: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcurand.so.10
2022-12-05 15:20:35.465979: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusolver.so.10
2022-12-05 15:20:35.465990: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusparse.so.10
2022-12-05 15:20:35.466014: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudnn.so.7
2022-12-05 15:20:35.466293: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1746] Adding visible gpu devices: 0
2022-12-05 15:20:35.467575: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudart.so.10.2
2022-12-05 15:20:37.547779: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1159] Device interconnect StreamExecutor with strength 1 edge matrix:
2022-12-05 15:20:37.547817: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1165]      0 
2022-12-05 15:20:37.547827: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1178] 0:   N 
2022-12-05 15:20:37.550029: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1304] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 14988 MB memory) -> physical GPU (device: 0, name: Tesla P100-PCIE-16GB, pci bus id: 0000:0b:00.0, compute capability: 6.0)
step: 50, loss: 0.077484, W: 0.264989, b: 0.704126
step: 100, loss: 0.077420, W: 0.264202, b: 0.709704
step: 150, loss: 0.077364, W: 0.263462, b: 0.714954
step: 200, loss: 0.077314, W: 0.262765, b: 0.719894
step: 250, loss: 0.077270, W: 0.262109, b: 0.724544
step: 300, loss: 0.077231, W: 0.261492, b: 0.728919
step: 350, loss: 0.077197, W: 0.260911, b: 0.733037
step: 400, loss: 0.077166, W: 0.260365, b: 0.736912
step: 450, loss: 0.077139, W: 0.259850, b: 0.740558
step: 500, loss: 0.077115, W: 0.259366, b: 0.743990
step: 550, loss: 0.077094, W: 0.258911, b: 0.747220
step: 600, loss: 0.077075, W: 0.258482, b: 0.750259
step: 650, loss: 0.077058, W: 0.258079, b: 0.753119
step: 700, loss: 0.077043, W: 0.257699, b: 0.755811
step: 750, loss: 0.077030, W: 0.257342, b: 0.758344
step: 800, loss: 0.077019, W: 0.257005, b: 0.760727
step: 850, loss: 0.077008, W: 0.256689, b: 0.762971
step: 900, loss: 0.076999, W: 0.256391, b: 0.765082
step: 950, loss: 0.076991, W: 0.256111, b: 0.767069
step: 1000, loss: 0.076984, W: 0.255847, b: 0.768938
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=5&local_job_id=1471449 by 8am on 2022/12/06.

```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
