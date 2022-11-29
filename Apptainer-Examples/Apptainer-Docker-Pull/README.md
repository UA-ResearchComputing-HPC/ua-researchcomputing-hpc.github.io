# Apptainer Docker Pull

[![](/Images/Download-Button.png)](Apptainer-Docker-Pull.tar.gz)

## About
You can use the pull command to download pre-built images from resources like Docker. This example pulls a container and executes it on a compute node in a batch script.

## Command Line Container Pull
This can be run in an interactive session prior to submitting your job. Apptainer is installed on HPC compute nodes so no module commands are necessary to access the software. 
```console
[netid@cpu37 ~]$ apptainer pull ./lolcow.sif docker://godlovedc/lolcow
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
. . . 
INFO:    Creating SIF file...
[netid@cpu37 ~]$ 
```

## Submission Script

```bash
#!/bin/bash
#SBATCH --job-name=Apptainer_Docker_Pull
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

apptainer run lolcow.sif
```

## Job Submission
```console
[netid@cpu37 ~]$ sbatch apptainer-docker-pull.slurm 
Submitted batch job 514776
```

## Output
```console
[netid@cpu37 ~]$ cat slurm-514776.out 
 ________________________________________
/ Your analyst has you mixed up with     \
| another patient. Don't believe a thing |
\ he tells you.                          /
 ----------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

-------
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
