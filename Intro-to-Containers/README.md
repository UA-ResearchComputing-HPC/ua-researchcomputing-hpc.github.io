# Intro to Containers (Under Construction)

<br>


<h2> Contents </h2>

<img src="shipping.jpg" alt="shipping" style="margin: 20px 12px 3px 0px; box-shadow: 10px 10px 5px grey; border: 5px solid #FBFCFC; border-radius: 4px;" align="right" height="500"/> 

<h3 style="color:#FBFCFC; background-color:#34495E;"> <a href="#overview" style="text-decoration=none; color:#FBFCFC;"> Overview </a> </h3>
<h3 style="color:#FBFCFC; background-color:#34495E;"> <a href="#system-basics" style="text-decoration=none; color:#FBFCFC;"> System Basics </a> </h3>

* [Logging In](#logging-in)
* [Working on the Command Line](#working-on-the-command-line)



<br>

* What is a container
* Docker and Singularity 
* Building/Using Containers
* GPUs
* MPI
* BioContainers

```console
[laptop ~]$ ssh netid@hpc.arizona.edu
[netid@gatekeeper ~]$ shell
(puma) [netid@wentletrap ~]$ ocelote
(ocelote) [netid@wentletrap ~]$ interactive
[netid@i16n2 ~]$ singularity help
[netid@i16n2 ~]$ singularity help build
```
> Note: look in .singularity for working files 

# Singularity on HPC - Creating

### Pull from Docker registry 
> Less reproducible -- image can change
```console
[netid@i16n2 ~]$ singularity pull docker://godlovedc/lolcow
```

### Pulls from a container library 
> More reproducible
```console
[netid@i16n2 ~]$ singularity pull library://sylabsed/examples/lolcow
```

### Builds from a Docker registry
> More options, converts to latest format, & needs a name
```console
[netid@i16n2 ~]$ singularity build lolcow.sif docker://godlovedc/lolcow
```

## Build remotely using Sylabs
### Steps:

1. Log into https://cloud.sylabs.io
2. Generate an access token (API key)
3. Start an interactive session:
```console
[netid@i16n2 ~]$ singularity remote login
[netid@i16n2 ~]$ singularity build --remote nersc.sif nersc.recipe
[netid@i16n2 ~]$ singularity run nersc.sif
```

# Running Singularity on HPC

```console
[netid@i16n2 ~]$ singularity shell lolcow_latest.sif
Singularity> 
Singularity> exit
```

```console
[netid@i16n2 ~]$ singularity run lolcow_latest.sif
[netid@i16n2 ~]$ # Or
[netid@i16n2 ~]$ ./lolcow_latest.sif
```

```console
[netid@i16n2 ~]$ singularity run library://sylabsed/examples/lolcow
INFO:    Using cached image
 _______________________________________
/ Wrinkles should merely indicate where \
| smiles have been.                     |
|                                       |
\ -- Mark Twain                         /
 ---------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

# Singularity on HPC - Running Batch
```sh
#!/bin/bash
#SBATCH --job-name=lolcow
#SBATCH --ntasks=1 
#SBATCH --nodes=1 
#SBATCH --mem=1gb 
#SBATCH --time=00:01:00 
#SBATCH --partition=standard 
#SBATCH --account=YOUR_GROUP 


cd /path/to/container
singularity run lolcow_latest.sif
```

# Singularity on HPC - Creating and Running
This example does not run cleanly, demonstrating that the container needs compatibility with the kernel on the compute node.

1. Copy the ```TFlow_example.py``` file from ```/contrib/singularity/nvidia```
```console
[netid@i16n8 ~]$ cp /contrib/singularity/nvidia/TFlow_example.py ./
```

```
[netid@i16n8 ~]$ singularity exec docker://tensorflow/tensorflow python -m TFlow_example.py
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
. . .
2022/08/29 11:48:00  info unpack layer: sha256:b3de072a17db29c655f9904867d2a9e9718ced4186f3213010e3a45db5a4d592
INFO:    Creating SIF file...
2022-08-29 11:59:43.480666: I tensorflow/core/platform/cpu_feature_guard.cc:193] This TensorFlow binary is optimized with oneAPI Deep Neural Network Library (oneDNN) to use the following CPU instructions in performance-critical operations:  AVX2 FMA
To enable them in other operations, rebuild TensorFlow with the appropriate compiler flags.
step: 50, loss: 0.130104, W: 0.382371, b: -0.128060
step: 100, loss: 0.124023, W: 0.374668, b: -0.073448
step: 150, loss: 0.118637, W: 0.367419, b: -0.022054
step: 200, loss: 0.113866, W: 0.360597, b: 0.026312
step: 250, loss: 0.109642, W: 0.354177, b: 0.071828
step: 300, loss: 0.105900, W: 0.348135, b: 0.114662
step: 350, loss: 0.102587, W: 0.342449, b: 0.154972
step: 400, loss: 0.099652, W: 0.337098, b: 0.192907
step: 450, loss: 0.097054, W: 0.332062, b: 0.228607
step: 500, loss: 0.094752, W: 0.327323, b: 0.262204
step: 550, loss: 0.092713, W: 0.322864, b: 0.293821
step: 600, loss: 0.090908, W: 0.318667, b: 0.323575
step: 650, loss: 0.089309, W: 0.314717, b: 0.351575
step: 700, loss: 0.087893, W: 0.311000, b: 0.377926
step: 750, loss: 0.086639, W: 0.307503, b: 0.402725
step: 800, loss: 0.085529, W: 0.304211, b: 0.426062
step: 850, loss: 0.084545, W: 0.301113, b: 0.448024
step: 900, loss: 0.083674, W: 0.298198, b: 0.468692
step: 950, loss: 0.082903, W: 0.295454, b: 0.488143
step: 1000, loss: 0.082219, W: 0.292872, b: 0.506447
[netid@i16n8 ~]$ 
```

# Singularity on HPC - File Paths
Access to your files outside the container: Binding
You automatically get ```/home```, ```/tmp```, ```/xdisk```, ```/groups```, and ```$PWD```
```console
[netid@i16n8 ~]$ echo "Hello from inside the container" > $HOME/hostfile.txt
[netid@i16n8 ~]$ singularity exec lolcow_latest.sif cat $HOME/hostfile.txt
Hello from inside the container
```

```console
[netid@i16n8 ~]$ echo "Drink milk (and never eat hamburgers)." > data/cow_advice.txt
[netid@i16n8 ~]$ singularity exec --bind $PWD/data:/mnt lolcow_latest.sif cat /mnt/cow_advice.txt
Drink milk (and never eat hamburgers).
```
Alternatives:
```console
$ singularity shell --bind /data my-container.sif
```
```console
$ export SINGULARITY_BINDPATH=/data
```

# GPUs and Containers
We show four ways to run tensorflow containers on HPC. We just use Tensorflow as an example
1. Containers as modules: caffe, pytorch, rapids, tensorflow, and theano
2. 2. Tensorflow examples from our Github
3. 3. Tensorflow directly from Nvidia
4. Tensorflow from DockerHub

# References
| Reference | Link |
| ----------| ---- |
| UA HPC Containers Documentation | https://public.confluence.arizona.edu/display/UAHPC/Containers |
| UA HPC Examples Github | https://ua-researchcomputing-hpc.github.io |
| NIH Containers Documentation | https://hpc.nih.gov/apps/singularity.html |
| Sylabs User Guide | https://sylabs.io/guides/3.5/user-guide/introduction.html |
| Sylabs Examples | https://github.com/sylabs/examples |
| TACC Container Basics | https://containers-at-tacc.readthedocs.io/en/latest/ |
| CyVerse Container Camp | ttps://cyverse-container-camp-workshop-2018.readthedocs-hosted.com |
| Tensorflow User Guide | https://www.tensorflow.org/tutorials/quickstart/beginner |
