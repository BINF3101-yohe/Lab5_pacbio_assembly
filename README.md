# Lab3_de_novo_assembly_part2

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

### Steb 3c - Submit your slurm script

Submit your slurm script 

```bash
sbatch abyss.slurm
```

Then you can check to make sure it is running using

```bash
squeue -u unccusername
```

## Step 4 - 






