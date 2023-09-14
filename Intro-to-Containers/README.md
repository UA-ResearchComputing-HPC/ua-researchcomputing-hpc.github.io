# Intro to Containers

<br>


<h2> Contents </h2>

<img src="shipping.jpg" alt="shipping" style="margin: 20px 12px 3px 0px; box-shadow: 10px 10px 5px grey; border: 5px solid #FBFCFC; border-radius: 4px;" align="right" height="500"/> 

<h3 style="color:#FBFCFC; background-color:#34495E;"> <a href="#creating-a-container" style="text-decoration=none; color:#FBFCFC;"> Creating a Container </a> </h3>

* [Pull from Docker registry](#pull-from-docker-registry)
* [Pull from container library](#pull-from-container-library)
* [Build from Docker registry](#build-from-docker-registry)


<h3 style="color:#FBFCFC; background-color:#34495E;"> <a href="#running-a-container-on-hpc" style="text-decoration=none; color:#FBFCFC;"> Running Apptainer on HPC </a> </h3>
* [Logging In](#logging-in)
* [Working on the Command Line](#working-on-the-command-line)

<br><br><br><br><br><br><br><br><br><br><br>


# Apptainer

> note that if you create many containers many gigibytes of cache files
> may be written  to ~/.apptainer.  The files can be safely deleted,
> so be careful not to fill your home directory.
<br>

First start an interactive session
```console
[laptop ~]$ ssh netid@hpc.arizona.edu
[netid@gatekeeper ~]$ shell
(puma) [netid@wentletrap ~]$ ocelote
(ocelote) [netid@wentletrap ~]$ interactive
[netid@i16n2 ~]$ apptainer help
[netid@i16n2 ~]$ apptainer help build
```

## Creating a Container

### Pull from Docker registry 
> Less reproducible -- image can change
```console
apptainer pull docker://godlovedc/lolcow
```

### Pull from container library 
> More reproducible
```console
apptainer pull library://sylabsed/examples/lolcow
```

### Build from Docker registry
> More options, converts to latest format, & needs a name
```console
apptainer build lolcow.sif docker://godlovedc/lolcow
```


## Running Apptainer on HPC

```apptainer shell``` opens a command prompt to the environment inside the container.
Try executing some commands.  Notice that although you are inside 
the container (a separate operating system), you will be able to 
list the contents of your home directory.
```console
[netid@i16n2 ~]$ apptainer shell lolcow_latest.sif
Apptainer> 
Apptainer> exit
```

```apptainer run``` executes the default run script within the container,
which was specified when the container was created.
```console
[netid@i16n2 ~]$ apptainer run lolcow_latest.sif
[netid@i16n2 ~]$ # Or
[netid@i16n2 ~]$ ./lolcow_latest.sif
```

```console
[netid@i16n2 ~]$ apptainer run library://sylabsed/examples/lolcow
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

## Apptainer on HPC as a Batch Job
No modules need to be loaded to execute a container in 
a Slurm batch submission.
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
apptainer run lolcow_latest.sif
```

## Apptainer on HPC - File Paths
Access to your files outside the container: Binding 
You automatically get ```/home```, ```/tmp```, ```/xdisk```, ```/groups```, and ```$PWD```
```console
[netid@i16n8 ~]$ echo "Hello from inside the container" > $HOME/hostfile.txt
[netid@i16n8 ~]$ apptainer exec lolcow_latest.sif cat $HOME/hostfile.txt
Hello from inside the container
```

```console
[netid@i16n8 ~]$ echo "Drink milk (and never eat hamburgers)." > data/cow_advice.txt
[netid@i16n8 ~]$ apptainer exec --bind $PWD/data:/mnt lolcow_latest.sif cat /mnt/cow_advice.txt
Drink milk (and never eat hamburgers).
```
Alternatives:
```console
$ apptainer shell --bind /data my-container.sif
```
```console
$ export APPTAINER_BINDPATH=/data
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
| NIH Containers Documentation | https://hpc.nih.gov/apps/Apptainer.html |
| Sylabs User Guide | https://sylabs.io/guides/3.5/user-guide/introduction.html |
| Sylabs Examples | https://github.com/sylabs/examples |
| TACC Container Basics | https://containers-at-tacc.readthedocs.io/en/latest/ |
| CyVerse Container Camp | ttps://cyverse-container-camp-workshop-2018.readthedocs-hosted.com |
| Tensorflow User Guide | https://www.tensorflow.org/tutorials/quickstart/beginner |
