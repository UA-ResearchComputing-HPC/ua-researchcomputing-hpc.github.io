# Singularity Docker Pull

[![](/Images/Download-Button.png)](Singularity-Docker-Pull.tar.gz)

## About
You can use the pull command to download pre-built images from resources like Docker. This example pulls a container and executes it on a compute node in a batch script.

## Command Line Container Pull
This can be run in an interactive session prior to submitting your job. Singularity is installed on HPC compute nodes so no module commands are necessary to access the software. 
```console
[netid@r1u03n1 ~]$ singularity pull ./lolcow.sif docker://godlovedc/lolcow
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
...
INFO:    Creating SIF file...
[netid@r1u03n1 ~]$ 
```

## Submission Script

```bash
#!/bin/bash
#SBATCH --job-name=Singularity_Docker_Pull
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

singularity run lolcow.sif
```

## Job Submission
```console
[netid@r1u03n1 ~]$ sbatch singularity-docker-pull.slurm 
Submitted batch job 1996677
```

## Output
```console
[netid@r1u03n1 ~]$ cat slurm-1996677.out 
 ____________________________________
/ This life is yours. Some of it was \
| given to you; the rest, you made   |
\ yourself.                          /
 ------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

-------
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
