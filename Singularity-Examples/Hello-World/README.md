# Apptainer Hello World

[![](/Images/Download-Button.png)](Hello-World.tar.gz)

## About 
You can use the pull or build commands to download pre-built images from resources like Singularity Hub or Docker Hub. In this example, we'll pull a container from Singularity Hub and execute it in a SLURM script.

## Command Line Container Pull
To retrieve the singularity image, you can pull it using ```apptainer pull```. This will save it locally to the filename and path provided (in this case, your working directory as hello-world.sif. Apptainer is available on every HPC compute node and does not need to be loaded with a module statement. On a compute node:
```console
[netid@cpu37 ~]$ apptainer pull ./hello-world.sif shub://vsoch/hello-world
INFO:    Downloading shub image
59.8MiB / 59.8MiB [========================================] 100 % 36.6 MiB/s 0s
[netid@cpu37 ~]$ 
```

## Submission Script

```bash
#!/bin/bash
#SBATCH --job-name=Singularity_Hello_World
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

apptainer run hello-world.sif
```

## Job Submission
```console
[netid@cpu37 ~]$ sbatch hello-world.slurm 
Submitted batch job 514773
```

## Output
```console
[netid@cpu37 ~]$ cat slurm-514773.out 
RaawwWWWWWRRRR!! Avocado!
```

----------
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
