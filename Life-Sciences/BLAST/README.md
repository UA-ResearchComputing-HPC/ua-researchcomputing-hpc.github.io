# BLAST Example

This blastp script constructs a blast database from a fasta file. In then uses 4 threads to blast an input sequence against the database. This script can be easily modified to run blastn, blastx, etc. by changing the executable program and database.

The included reference fasta file was downloades from an NCBI ftp repository.

For more information on BLAST, see:
* [BLAST Quick Start](https://www.ncbi.nlm.nih.gov/books/NBK279680/)
* [BLAST Software and Databases](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs&DOC_TYPE=Download)

## Script Example
[Click here to download example](BLAST_EXAMPLE.tar.gz)

To run this example, click the link above to download the tar file and unpack it in your HPC account. Inside, you will find ```blast_example.slurm```. This script requires minimal edits to run. Replace ```YOUR_GROUP``` with your own group's name (this can be found using the command ```va``` if you are unsure).
```
#!/bin/bash
#SBATCH --job-name=BLAST-Example
#SBATCH --ntasks=4
#SBATCH --nodes=1             
#SBATCH --time=00:01:00   
#SBATCH --partition=standard
#SBATCH --account=YOUR_GROUP
module load blast

makeblastdb -in human.1.protein.faa  -dbtype prot
time blastp -db ./human.1.protein.faa -query query.faa -out sample_blast_run.out -evalue 1e-30 -num_descriptions 10 -num_alignments 10 -num_threads 4
```

## Script Submission
```
(puma) [netid@junonia ~]$ sbatch example_blast_job.slurm
Submitted batch job 1694194
```

## Output files
```
(puma) [netid@junonia ~]$ ls *.out *human*.faa.*
sample_blast_run.out     slurm-1694194.out        human.1.protein.faa.pdb
human.1.protein.faa.phr  human.1.protein.faa.pin  human.1.protein.faa.pot
human.1.protein.faa.psq  human.1.protein.faa.ptf  human.1.protein.faa.pto
```

*****
[back](../)
