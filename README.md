

# metagenomic-pipeline

#### Definition of Metagenomic 
This is shotgun meta genomic. We only look at a small fraction of the samples and DNA. 
Metagenomic answers question about whfo is there and what they are doing there. 
![pic1](https://github.com/sheewani/metagenomic-pipeline/blob/cbfb50041d47373acf04fafa330854e710873e5d/Screenshot%202025-01-15%20at%209.44.24%E2%80%AFAM.png)


Image Source:https://astrobiomike.github.io/misc/amplicon_and_metagen

## Download the datasets
In this project 60 raw sequences were dowloaded from NCBI using SRA accesssion number through Inline `prefetch` Inline `fastqdump`. 
**Make sure you install SRA toolkit**

Indented code

    // Some comments
    $ fastq-dump --gzip --skip-technical --readids --read-filter pass --dumpbase --split-3 --clip --outdir path/to/reads/ SRR_ID

this command is to download and process SRA data, ensuring it is properly filtered, compressed, and saved in the desired location, ready for downstream analysis.

---
__Important Links in this section :)__

- __[fastqdump](https://rnnh.github.io/bioinfo-notebook/docs/fastq-dump.html)__ - Fastq-dump documention
- __[SRA-Toolkit](https://github.com/ncbi/sra-tools)__ - The NCBI SRA (Sequence Read Archive)

---