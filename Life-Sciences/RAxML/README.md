# RAxML Example
[![](/Images/Download-Button.png)](RAxML.tar.gz)

## About
For information on RAxML, see [the RAxML documentation](https://cme.h-its.org/exelixis/web/software/raxml/#documentation)

## Example
```bash
#!/bin/bash
#SBATCH --job-name=RAxML-Example
#SBATCH --ntasks=8
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP

module load raxml gnu8 openmpi3
date
mpirun -np $SLURM_NTASKS raxmlHPC-MPI-AVX2 -s phylip_sample.phy -n output_tree.tre -f a -m PROTCATBLOSUM62 -x 12345 -p 12345 -N 1000
date
```

## Script Information

This RAxML script uses 8 CPUs and sets the following parameters:

| Command | Description |
| ------- | ----------- |
|```-s phylip_sample.phy```| Input file |
|```-n output_tree.tre```| Output file |
| ```-f a``` | Select algorithm. In this case, rapid boostrap analyses and search for best-scoring ML tree in one program run|
|```-m PROTCATBLOSUM62```|Model of substitution|
|```-x 12345```| Specify an integer number (random seed) and turn on rapid bootstrapping|
|```-N 1000```| Specify the number of alternative runs on distinct starting trees|


## Script Submission
```console
(puma) [netid@junonia ~]$ sbatch raxml_example.slurm 
Submitted batch job 1694264
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
