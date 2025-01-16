

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
__Important Links in this section :)__

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
prefetch SRR#
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
__Important Links in this section :)__

- __[FastQ](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)__ - Quality Control before trimming and filtering since each data is different 
- __[Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)__ - read trimming tool for Illumina NGS

---


## Assembly