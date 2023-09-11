# Lab3_de_novo_assembly_part2

# Outline

[Introduction](#introduction)

[Step 1 - Create a clean folder to work in](#step-1---create-a-clean-folder-to-work-in)

[Step 2 - Identify kmer length](#step-2---identify-kmer-length)

[Lab Question 1](#lq-1)

[Step 3 - Assemble your genome](#step-3---assemble-your-genome)

[Step 4 - Analyze our genome quality](#step-4---analyze-our-genome-quality)

[Lab Question 2](#lq-2)

[Step 5 - Filter the small contigs from our genome](#step-5---filter-the-small-contigs-from-our-genome)

[Lab Question 3](#lq-3)

## Introduction

Last lab we downloaded the raw sequencing reads for a species of budding yeast. Then we trimmed the reads to remove adaptor sequences and any other reads that were low quality. 

We now need to assemble those DNA fragments into a genome assembly. Most of the species you are studying _have never been sequenced before!_ Therefore we need to do a _de novo_ assembly. We will go over what that means more in depth in class. Briefly, we need to find overlapping reads and put them together like a puzzle. 

![Assembly-768x360](https://github.com/alabella19/Lab3_de_novo_assembly_part2/assets/47755288/cfbf47dc-39f9-4aec-92e1-bc79e19e399b)


&nbsp;
## Step 1 - Create a clean folder to work in

You may have made a new folder for lab2 or you may have worked in your home directory. This time let's create a new folder and copy our sequences into that folder

```bash
mkdir lab_3
```

Then copy your processed read files into the lab_3 directory. If you saved yours somewhere else you should amend this command

```bash
cp lab_2/SRR6475892/SRR6475892_1_paired.fastq.gz lab_3/.
cp lab_2/SRR6475892/SRR6475892_2_paired.fastq.gz lab_3/.
```
This may take a second. Once it is done, navigate into the lab_3 directory using ```cd```
&nbsp;
## Step 2 - Identify kmer length

The first step in our genome assembly is to identify what **kmer** length we will be using

You can think of a **kmer** as being the length of the word you are going to use to find matches while building your puzzle. We are going to go over this more carefully in class as well 

The kmer length can impact how well the genome assembly works, so we want to figure that out first

To determine kmer lenght we will use **kmergenie**

kmergenie _is not_ already installed on the computing cluster so we will need to do that first. Luckily, this program comes in a neat package called an Anaconda Environment. 

### Step 2a - Activiate anaconda

Type this command to activate anaconda

```bash
module load anaconda3
```

### Step 2b - Install the kmergenie package

```bash
conda create -n kmergenie_env -c bioconda kmergenie
```

You will then be prompted to Proceed with the installation

**Type y and press enter**

A bunch of packages will then be installed. It will look something like this:

![image](https://github.com/BINF-3101/Lab3_de_novo_assembly_part2/assets/47755288/796669e6-64c6-4b8c-b57d-d5de1724b178)


_What did we just do?_

Often people will develop programs that require dependencies. Dependencies are _other_ programs required to run the final product. Keeping track of these dependencies can be difficult. 

So, we built an "environment" that is a little bubble we can work in where all the necessary programs are installed. You need to "activate" our bubble. We will activate that bubble next. 

### Step 2c - Activate our anaconda environment

Whenever you want to open our environment you must have anaconda loaded.

```bash
module load anaconda3
```

Then activate the environment

```bash
conda activate kmergenie_env
```

You will see that we now have our conda environment bubble in parentheses before our terminal name. 

![image](https://github.com/BINF-3101/Lab3_de_novo_assembly_part2/assets/47755288/4008f570-7423-4bc6-8492-cc0dee48bd8b)

We only need to have this active when we are using kmergenie 

### Step 2d - Run kmergenie

This is the command to run kmergenie on our forward set of reads. We will only run it on the forward set as the optimal kmer is likely to be the same

```bash
kmergenie SRR6475892_1_paired.fastq.gz -l 21 -k 121 -s 2 -o out_file.txt
```

Here are what all of the options mean
```-l 21``` - smallest k-mer size to test
```-k 121``` - largest k-mer size to est
```-s 2``` - the interval to test k-mers at. We jump by 2 every time we test
```-o kmergenie_SSR6475892``` prefix to all of our output files to help keep us organized

This will take a moment! So take some time to review the next few steps 

### Step 2e - Find our optimal k-mer size

You will get a printout from you run that contains your optimal k-mer size. Write it down! You will need it for the next step. 

![image](https://github.com/BINF-3101/Lab3_de_novo_assembly_part2/assets/47755288/e16a4883-1e12-4f6d-a3ea-458546df2f12)

## LQ 1

What k-mer size was chosen for you by kmergenie?

&nbsp;
## Step 3 - Assemble your genome

Now we will use a genome assembler called ABySS (Assembly By Short Sequences). We will go more into depth as to how this assembler works in class

### Step 3a - Copy the slurm script

Copy the slurm script from the class folder to your directory where you have the sequence files

```bash
cp /projects/class/binf3101_001/abyss.slurm .
```

**TIP** The "." in the command above means "here". You are copying (cp) the first file (/projects/class/binf3101_001/abyss.slurm) to here (.)

### Step 3b - Edit the slurm script

You will need to edit the slurm script to include your k-mer number after "k=" and change SRRNUMBER to your SRR number

![image](https://github.com/BINF-3101/Lab3_de_novo_assembly_part2/assets/47755288/c1f4d46b-63ab-4753-a730-9ac82a80433a)

Refer to lab #1 if you need help remembering how to edit a file using vi or nano. 

You can also check to make sure your edits are correct using ```cat abyss.slurm```

### Step 3c - Submit your slurm script

Submit your slurm script 

```bash
sbatch abyss.slurm
```

Then you can check to make sure it is running using

```bash
squeue -u unccusername
```

When the assembly is done you will see a file called "slurm-number.out" that will be the report of the run

You will also see a lot of files that end in dot, path, fa, hist, fai, dot1 and more

Our assembly is in this file: SRR6475892-**contigs.fa**

### Step 3d - Make a copy of your final file and rename it

Abyss leaves us with a complicated set of files which have aliases (additional names)

To prevent any issues we are going to make a copy 

```bash
cp SRR6475892-contigs.fa SRR6475892-contigs.v1.fa
```

## Step 4 - Analyze our genome quality 

We are going to use a program called QUAST to assess our genome quality. Quast does not like the packages we previously loaded. So you can either log out of the cluster and log back in OR we can leave the bubble

To reset our terminal we will need to unload our anaconda environment

```bash
conda deactivate
```

Then we want to unload all of our modules

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
quast.py SRR6475892-contigs.v1.fa
```


### Step 4c - Look at our genome quality results

You will find the genome quality results in a folder called ```quast_results```

Within that folder, there will be additional folders for each analysis you have run (if you ran it more than once) labeled by the date. 

Quast provides us with graphics and text output. If you want to look at the plots they are in the ```basic_stats``` folder and you will need to download them to look at this. 

All our results are saved in a file called ```report.txt```

Navigate into the folder containing your results and open/cat the report.txt file


## LQ 2

Report the following genome statistics in the Canvas assignment
- contigs under 1000 bp ( contigs (>= 0b bp) )
- size of the largest contig
- total length of the genome
- N50 value

## Step 5 - Filter the small contigs from our genome 

In the Quast output you should see a line that says "All statistics are based on contigs of size >= 500 bp". We want to remove the small contigs from our genome. We will use a custom python script to do that

### Step 5a - Copy the script from the class folder

Copy the script from the class folder
```bash
cp /projects/class/binf3101_001/filter_by_length.py .
```

This will copy the script to your current directory

### Step 5b - Find largest contig size

You will need to look at your Quast results and **find the size of your largest contig**

### Step 5c - Filter your genome

Now we will filter the genome to remove contigs that are less than 500bp long. 

**If you are getting "cannot import matplotlib" you must load anaconda3**

```bash
module load anaconda3
python filter_by_length.py SRR6475892-contigs.v1.fa SRR6475892-contigs.v2.fa 500 **294944**
```

Don't forget! You will need to change the larger number to your biggest contig size. 

## LQ 3

How many contigs (sequences) are in the final version of your genome? _Hint: we have used grep to count this in the past_

## Assembled genome!

You now have now conducted a _de novo_ genome assembly directly from Illumina sequencing reads! Your genome is now saved in SRR6475892-contigs.v2.fa
