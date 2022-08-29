# Coming Soon

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
