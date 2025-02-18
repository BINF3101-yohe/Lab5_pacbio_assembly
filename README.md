# Lab5_pacBio_assembly

# Outline

[Introduction](#introduction)

[Step 1 - Create a clean folder to work in](#step-1---create-a-clean-folder-to-work-in)

[Step 2 - Run _de novo_ assembly with Flye](#step-2---run-de-novo-assembly-with-flye))

[Lab Question 1](#lq-1)

[Step 3 - Assemble your genome](#step-3---assemble-your-genome)

[Step 4 - Analyze our genome quality](#step-4---analyze-our-genome-quality)




## Introduction

Last lab we downloaded the raw sequencing reads for a species of budding yeast. Then we trimmed the reads to remove adaptor sequences and any other reads that were low quality. 

We now need to assemble those DNA fragments into a genome assembly. Most of the species you are studying _have never been sequenced before!_ Therefore we need to do a _de novo_ assembly. We will go over what that means more in depth in class. Briefly, we need to find overlapping reads and put them together like a puzzle. 



&nbsp;
## Step 1 - Create a clean folder to work in

Let's create a new folder and download our PacBio fastq fils into that folder

```bash
mkdir lab_5
```

Then fetch your read files in the lab_5 directory. If you saved yours somewhere else, you should amend this command.

```bash
cd lab_5
wget https://zenodo.org/records/5702408/files/SRR8534473_subreads.fastq.gz
wget https://zenodo.org/records/5702408/files/SRR8534474_subreads.fastq.gz
wget https://zenodo.org/records/5702408/files/SRR8534475_subreads.fastq.gz
```
This may take a few minutes. Once it is done, check the three have downloaded in the lab_5 directory using ```ls```
&nbsp;

Give the files the correct permission if they are red.
```
chmod 777 *gz
```

Always look at your data. Let's see how many reads are in these folders:
The command to count the number of reads in a fastq file is as follows:

```
/projects/class/binf3101_001/count_fastq.sh SRR8534473_subreads.fastq.gz
```

## LQ 1

How would I alter this command if I wanted to count the number of reads in all fastq.gz files in my folder?
Please submit the modified command.
&nbsp;

Let's see how different PacBio data is from Illumina data. Remember illumina data pretty much had 240-260bp length reads.

Run the following commands on one of your fastq files.

```
awk 'NR%4 == 2 {lengths[length($0)]++} END {for (l in lengths) {print l, lengths[l]}}' SRR8534473_subreads.fastq.gz 
```
This is going to print a lot of output, but it will tell you the length of each read. Scroll to the top and see how long the longest read is. Pretty cool, right?! ;-)
Even though there are less reads, many reads are much longer than 250bp!

## LQ 2
How many reads are in  SRR8534473_subreads.fastq.gz?
What is the length of the longest read in SRR8534473_subreads.fastq.gz?

&nbsp;


## Step 2 - Run _de novo_ assembly with Flye

Flye is designed for a wide range of datasets, from small bacterial projects to large mammalian-scale assemblies. The package represents a complete pipeline: it takes raw PacBio / ONT reads as input and outputs polished contigs.


### Step 2a - Explore what flye can do.

We are going to grow up into more advanced and independent bioinformaticians where I no longer give you the exact slurm script. Yay!

Inspect what flye is capable of and all of the available options.
```bash
module load flye
flye --help
```
![Screenshot 2025-02-11 at 3 51 59 PM](https://github.com/user-attachments/assets/e8901795-2351-4955-b3f9-eca897e5c1c0)

A lot, right?!

The relevant commands we need to include are ```--pac-bio-raw```, set the ```--iterations``` to "1", and set the output directory as ```--out-dir SRR853447_pacbio_assembly```.
Make sure to include all three of your sets of reads in your command!


### Step 2b - Edit the slurm script.

Edit ```flye.slurm``` included in the github here or make your own file by copying the base of the script below. 
Refer to lab #1 if you need help remembering how to edit a file using vi or nano.

```bash
#!/bin/bash 

#SBATCH --partition=Centaurus
#SBATCH --job-name=flye_job
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=48:00:00
#SBATCH --mem-per-cpu=200G

echo "======================================================"
echo "Start Time  : $(date)"
echo "Submit Dir  : $SLURM_SUBMIT_DIR"
echo "Job ID/Name : $SLURM_JOBID : $SLURM_JOB_NAME"
echo "Node List   : $SLURM_JOB_NODELIST"
echo "Num Tasks   : $SLURM_NTASKS total [$SLURM_NNODES nodes @ $SLURM_CPUS_ON_NODE CPUs/node]"
echo "======================================================"
echo ""


mkdir tmp
export TMPDIR=$SLURM_SUBMIT_DIR/tmp

module load flye
cd $SLURM_SUBMIT_DIR

#uncomment this line and put flye command here :)



echo ""
echo "======================================================"
echo "End Time   : $(date)"
echo "======================================================"
```


## Step 3 - Submit your slurm script

Submit your slurm script 

```bash
sbatch flye.slurm
```

Then you can check to make sure it is running using

```bash
squeue -u unccusername
```



This process will have to run over night. Be patient! Check to see its working or if it has crashed.

```bash
tail slurmXXX.out
```

The tool produces four datasets: consensus, assembly graph, graphical fragment assembly and assembly info.

The first dataset (consensus) is a fasta file containing the final assembly (1461 contigs). You may notice that the result (contigs number) you obtained is sligthy different from the one presented here. This is due to the Flye assembly algorithm which doesn’t always give the eact same results.

The second and third dataset are assembly graph files. These graphs are used to represent the final assembly of a genome, they are based on reads and their overlap information. Some tools such as Bandage allow to visualize the assembly graph.

The fourth dataset is a tabular file (assembly_info) containing extra information about contigs/scaffolds.

Cool!

Our assembly is in the fasta file of the "consensus" output.

### Step 3d - Make a copy of your final file and rename it

Abyss leaves us with a complicated set of files which have aliases (additional names)

To prevent any issues we are going to make a copy 

```bash
cp consensus.fa consensus.v1.fa
```

## Step 4 - Analyze our genome quality 

We are going to use a program called QUAST to assess our genome quality. Quast does not like the packages we previously loaded. So you can either log out of the cluster and log back in OR we can leave the bubble

To reset our terminal, we will need to unload everything


```bash
module purge
```

**TIP** The module purge command is very useful. It's a good idea to run it if you are having problems with 


### Step 4a - load the genome quality assessment program

We will be using Quast to look at our genome quality. It is already installed on the cluster, so we need to load the module

```bash
module load quast
```


### Step 4b - Analyze your genome quality

To run quast on our genome assembly use the command below

```bash
quast.py consesnus.fa
```


### Step 4c - Look at our genome quality results

You will find the genome quality results in a folder called ```quast_results```

Within that folder, there will be additional folders for each analysis you have run (if you ran it more than once) labeled by the date. 

Quast provides us with graphics and text output. If you want to look at the plots they are in the ```basic_stats``` folder and you will need to download them to look at this. 


## LQ3

How many contigs assembled in the consensus?
&nbsp;

All our results are saved in a file called ```report.txt```

Navigate into the folder containing your results and open/cat the report.txt file


## LQ 4

Report the following genome statistics in the Canvas assignment
- contigs under 1000 bp ( contigs (>= 0b bp) )
- size of the largest contig
- total length of the genome
- N50 value
&nbsp;

## Assembled genome!

You now have now conducted a _de novo_ genome assembly directly from PacBio sequencing reads! But whose genome was it?

## LQ 5
Based on the SRA information.
What sepecies genome did we sequence? What did you learn about its ecology?




