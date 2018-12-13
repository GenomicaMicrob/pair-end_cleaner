### Release [v1.0.2](https://github.com/GenomicaMicrob/pair-end_cleaner/releases/latest)

# pair-end_cleaner
Script to unzip, clean, assemble, and convert illumina pair-end fastq files in all subdirectories for 16S (V3, V4 and V3-V4 regions) and 18S (V9 region) amplicon data .

This script is intended to rapidly process many fastq files without any extra commands and produce fasta files ready for downstream analyses, such as chimera filtering and taxon classification. It is already fine-tuned for this kind of files and 16S/18S regions, and no extra commands should be necessary.

Estimated region sizes (nucleotides) including primer sequences:

| Region | Size |
| --- | --- |
| 16S_V3 | 200 |
| 16S_V4 | 292 |
| 16S_V3-V4 | 474 |
| 18S_V9 | 173 |
   

We have tested it extensively with files resulting of sequencing the V3 or V4 regions of the 16S with the Miniseq (2X150), all other regions should be OK too. 

**The script will:**
1. Uncompress the files.
2. Trims low quality bases (Q<20).
3. Filters out short sequences (below the optimum for each variable region).
4. Assembles both sequences.
5. Converts from fastq to fasta.
6. Renames the resulting sequences with the name of the sample only, it erases useless info, for example:
  for files of a sample named *sample01* (sample01_S01_L001_R1_001.fastq.gz, sample01_S01_L001_R2_001.fastq.gz) it will produce only sample01.
7. Renames all fasta header of a each file with the sample name and a consecutive number (>sample01_001, >sample01_002, etc.).
8. Moves all resulting fasta files to a new folder named `assembled.current_date_time`, creates a report, a log file, and a csv file with the number of raw sequences processed, clean, assembled and low quality sequences per sample.

## Dependencies
To clean the sequences: [cutadapt](https://github.com/marcelm/cutadapt), for installation instructions click [here](https://cutadapt.readthedocs.io/en/stable/installation.html#id1).

To assemble the pair-end sequences: [pear](https://sco.h-its.org/exelixis/web/software/pear/doc.html).

## Download and install
The best way to get pair-end_cleaner is to clone this repository directly to your linux:

1. Download the latest [release](https://github.com/GenomicaMicrob/pair-end_cleaner/releases/latest).
2. Make the script executable: `chmod +x pair-end_cleaner.v1.0.2.sh`.
3. Copy pair-end_cleaner.sh to an appropiate directory in your linux system, `/usr/bin/` is a good choice to have it available for everyone: `sudo cp pair-end_cleaner.v1.0.2sh /usr/bin/`.
4. Log out and log in in order that execute it.

## Format of the sequence files
Usually, the Illumina sequencers produce the resulting files in the following format:

    main_directory
        sample01 
           sample01_S01_L001_R1_001.fastq.gz
           sample01_S01_L001_R2_001.fastq.gz
        sample02
           sample02_S02_L001_R1_001.fastq.gz
           sample02_S02_L001_R2_001.fastq.gz
Sample results are nested in the main directory and in their corresponding subdirectory, they are compressed (.gz), and have a consecutive letter and number in the file name (_S01, _S02, etc.). Of course, since they are pair-end, we have two files per sample, one with the forward sequence (_L001_**R1**_001) and the other with the reverse sequence (_L001_**R2**_001).

All this information is used by the script to process the files, so please do not change any of this, use it as they are downloaded from the sequencer, or from BASESPACE.

**IMPORTANT.** Make sure you have only subdirectories with fastq sequences in the directory where you want to execute this script; it will try to process any other subdirectory and since it will not find any fastq or fastq.gz files in it, it will produce an error. This error is not critical and the script should process all valid subdirectories without problems.

## Usage
Go to the directory where you have all your subdirectories with the raw fastq files and type:

`pair-end_cleaner.v1.0.2.sh`

It will present the following options:

    Script to unzip, clean, assemble, convert, and rename
     Illumina pair-end fastq files in all 3 subdirectories.

    Select a region for
     16S:
         1 V3
         2 V4
         3 V3-V4
     18S:
         4 V9
         x exit
        
Just type the number for the region you have sequenced, or x to exit.

For help, type `pair-end_cleaner.v1.0.2.sh -h`

For version and dependencies, `pair-end_cleaner.v1.0.2.sh -v`

## Configuration

For configuration details, `pair-end_cleaner.v1.0.2.sh -c`

To change the main configuration parameters (CPUs and Quality score) edit the script's code. Just change the values after the equal sign ( = ) in the 'configuration' section at the top of the script.

*Example*

**CPUS=4**
>Number of cores to be used. This parameter only affects PEAR.

**Q=20**
>phred quality score to be used (one probable error per x bases), this parameters is for cutadapt.
