

# metagenomic-pipeline
This pipeline is inspired by my master's thesis at Cal State LA. Given the unique ecological and geological aspects of my samples, which are from the Mojave Desert, I intend to focus on identifying light-related genes in these samples. I will filter low-quality reads, perform assembly, identify open reading frames associated with light-related genes, count the predicted proteins, and construct a phylogenetic tree.


#### Definition of Metagenomic 
This is shotgun metagenomic. We only look at a small fraction of the samples and DNA. 
Metagenomic answers questions about what is there and what they are doing there. 
![pic1](https://github.com/sheewani/metagenomic-pipeline/blob/cbfb50041d47373acf04fafa330854e710873e5d/Screenshot%202025-01-15%20at%209.44.24%E2%80%AFAM.png)


Image Source:https://astrobiomike.github.io/misc/amplicon_and_metagen

## Download the datasets
In this project 60 raw sequences were dowloaded from NCBI using SRA accesssion number through `prefetch`  `fastqdump`. 
**Make sure you install SRA toolkit**


   
    $ fastq-dump --gzip --skip-technical --readids --read-filter pass --dumpbase --split-3 --clip --outdir path/to/reads/ SRR_ID

this command is to download and process SRA data, ensuring it is properly filtered, compressed, and saved in the desired location, ready for downstream analysis.

---
__Important links in this section :)__

- __[fastqdump](https://rnnh.github.io/bioinfo-notebook/docs/fastq-dump.html)__ - Fastq-dump documention
- __[SRA-Toolkit](https://github.com/ncbi/sra-tools)__ - The NCBI SRA (Sequence Read Archive)

---

This pipeline is not finished yet, the analysis will continue and this pipeline is being updated currently. 


With **one** sample SRR# 
```
prefetch SRR#
```
then fastq-dump, the SRR accession numbers should found under project name at NCBI under samples 
```
fasterq-dump SRR#
```

For loop for more than one metagenome you can process it faster by writing a for loop in bash download all the samples in this case 60 samples. 
It is always a good practice to write all the ID names in a seperate files (.txt) for varieties command. Here we can use it for parallel download. 

```
for i in$(cat <SRR list>.txt) do ;  prefetch $i ; fastq-dump --splitfiles $i
```
Here my list.txt file is the file containg all of my samples id manes and use to to  processes a list of Sequence Read Archive (SRA) accession numbers (e.g., SRR1234567, SRR1234568) from a file named <SRR list>.txt. 

1. Reads the file **<SRR list>**.txt line by line.
2. Downloads the **SRA** file corresponding to each accession number using prefetch.
3. Converts the SRA file into **FASTQ format** with fastq-dump, **splitting** paired-end reads into separate files if applicable.
4. Repeats this process for every accession number in the list.

## Quality trimming/filtering

Typically, sequencing facilities provide sequencing data in FASTQ files. The FASTQ format consists of four lines per sequence: (1) the sequence identifier (header) prefixed with an "@" symbol, (2) the sequence itself, (3) a "+" symbol (sometimes followed by a repeat of the header), and (4) quality scores for each base. With Illumina sequencing, these quality scores indicate the software's confidence in each base call. Although this system isn’t flawless—factors like polymerase errors and systematic biases can impact accuracy without being reflected in the quality scores—performing quality-based filtering remains crucial.

A highly user-friendly tool, **FastQC**, provides a comprehensive overview of data quality. It can identify common issues and guide your quality-filtering decisions effectively.

most often for quality filtering due to its flexibility would be **Trimmomatic**.

---
__Important links in this section :)__

- __[FastQ](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)__ - Quality Control before trimming and filtering since each data is different 
- __[Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)__ - read trimming tool for Illumina NGS

---


## Assembly
Assembly is basically like a puzzle that has so many pieces and you have to build it up and guess which piece belong to where. There are varites of assembler are out there and each has their own pros and cons. Here I will intriduce two assemblers which are more popular for microbial genomics including metagenomics. 

One of them is MEGAHIT which is a denovo assembler meaning without templates. It is designed for large and complex metagenomic datasets, using a succinct de Bruijn graph (SdBG) approach for memory efficiency and speed. It begins by preprocessing reads to remove low-quality sequences and adapters, followed by extracting k-mers (substrings of length \( k \)) across multiple \( k \)-mer sizes (multi-\( k \) strategy) to improve assembly accuracy. The SdBG efficiently represents overlaps between k-mers, reducing memory usage. MEGAHIT simplifies the graph by removing tips (dead-ends), bubbles (parallel paths), and collapsing repeats, ensuring a cleaner assembly. Iterative assembly refines the graph using smaller \( k \)-mers for sensitivity to low-abundance reads and larger \( k \)-mers for resolving repetitive regions. Contigs are then constructed from the simplified graph and output in FASTA format. Its multi-threading capability enhances speed, and the use of multi-\( k \) improves assembly quality for datasets with varying coverage, making MEGAHIT ideal for metagenomic studies on standard computational resources.

megahit installation 
```
conda install -c bioconda megahit
```

```
# Generate a sequence of numbers from 1 to 60 and run MEGAHIT in parallel for each sample
seq 60 | parallel \
  megahit \
    -1 /data/Biocrust/2024_Clean/Raw_Reads/ordered_fastq_raws/Trimmed/{}_R1P.fastq \  # Forward reads (R1)
    -2 /data/Biocrust/2024_Clean/Raw_Reads/ordered_fastq_raws/Trimmed/{}_R2P.fastq \  # Reverse reads (R2)
    -o {}_Megahit_out  # Output directory for each sample
```

1. seq 60: Generates numbers 1 through 60, representing sample identifiers.
2. parallel: Runs MEGAHIT commands concurrently for multiple samples.
3. -1 and -2: Paths to forward (R1P.fastq) and reverse (R2P.fastq) read files.
The {} placeholder is replaced with numbers from seq 60 (e.g., 1_R1P.fastq, 2_R1P.fastq).
4. -o: Specifies output directories named {sample_number}_Megahit_out for each dataset.

This command needs to be run on screen since it can takes couple days its a huge datasets: 1.5 TB


---
__Important links in this section :)__

- __[Megahit](https://academic.oup.com/bioinformatics/article/31/10/1674/177884__) - Quality Control before trimming and filtering since each data is different 
- __[Megahit GitHub](https://github.com/voutcn/megahit)__ - read trimming tool for Illumina NGS

---
When working on large datasets and running an analysis, usually could take hours or even days. So knowing running analysis on **screen** is an important knowledge and tool to use. Where you're running a lengthy task on a remote machine, and then your connection drops, causing the SSH session to terminate and your progress to be lost. There is a utility called `screen` that allows us to resume the sessions.

Screen, also known as GNU Screen, is a terminal multiplexer. This means you can start a screen session and open multiple windows (virtual terminals) within it. The processes running in Screen will keep running even if their window is not visible, and will continue to do so even if you get disconnected.

The screen package is pre-installed on most Linux distros nowadays. You can check if it is installed on your system by typing: 

  
    screen --version


to see the lists of attached screen
  

    screen -ls







After assembling the data, I will search for open reading frames of genes that encode light-related pathways. This exploration stems from my curiosity about how bacteria survive in such extreme environments. I aim to uncover the mechanisms of energy production, primarily focusing on **photosynthesis** as the main pathway, along with alternative, less energetically demanding mechanisms. Notably, **photosynthesis** is highly energy-intensive for microbial species, making these alternative pathways particularly intriguing.









This pipeline is not finished yet, the analysis will continue and this pipeline is being updated currently. 