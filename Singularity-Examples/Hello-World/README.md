# Singularity Hello World

[![](/Images/Download-Button.png)](Hello-World.tar.gz)

## About 
You can use the pull or build commands to download pre-built images from resources like Singularity Hub or Docker Hub. In this example, we'll pull a container from Singularity Hub and execute it in a SLURM script.

## Command Line Container Pull
To retrieve the singularity image, you can pull it using ```singularity pull```. This will save it locally to the filename and path provided (in this case, your working directory as hello-world.sif. Singularity is available on every HPC compute node and does not need to be loaded with a module statement. On a compute node:
```
[netid@r1u03n2 ~]$ singularity pull ./hello-world.sif shub://vsoch/hello-world
INFO:    Downloading shub image
59.8MiB / 59.8MiB [========================================] 100 % 14.0 MiB/s 0s
```

## Submission Script

```
#!/bin/bash
#SBATCH --job-name=Singularity_Hello_World
#SBATCH --ntasks=1
#SBATCH --nodes=1             
#SBATCH --mem=1gb                    
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

singularity run hello-world.sif
```

## Job Submission
```
[netid@r1u03n2 ~]$ sbatch hello-world.slurm 
Submitted batch job 1996576
```

## Output
```
[netid@r1u03n2 ~]$ cat slurm-1996576.out 
RaawwWWWWWRRRR!! Avocado!
```

----------
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
