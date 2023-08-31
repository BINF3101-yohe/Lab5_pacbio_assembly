# Lab3_de_novo_assembly_part2

Last lab we downloaded the raw sequencing reads for a species of budding yeast. Then we trimmed the reads to remove adaptor sequences and any other reads that were low quality. 

We now need to assemble those DNA fragments into a genome assembly. Most of the species you are studying _have never been sequenced before!_ Therefore we need to do a _de novo_ assembly. We will go over what that means more in depth in class. Briefly, we need to find overlapping reads and put them together like a puzzle. 

![Assembly-768x360](https://github.com/alabella19/Lab3_de_novo_assembly_part2/assets/47755288/cfbf47dc-39f9-4aec-92e1-bc79e19e399b)

## Step 1 - Identify kmer length

The first step in our genome assembly is to identify what **kmer** length we will be using

You can think of a **kmer** as being the length of the word you are going to use to find matches while building your puzzle. We are going to go over this more carefully in class as well 

The kmer length can impact how well the genome assembly works, so we want to figure that out first

To determine kmer lenght we will use **kmergenie**

kmergenie _is not_ already installed on the computing cluster so we will need to do that first. Luckily, this program comes in a neat package called an Anaconda Environment. 

### Step 1a - Activiate anaconda

Type this command to activate anaconda

```bash
module load anaconda3
```

### Step 1b - Install the kmergenie package

```bash
conda create -n kmergenie_env -c bioconda kmergenie
```

A bunch of 
