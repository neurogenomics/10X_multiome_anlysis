# 10X_multiome_anlysis
Simple straightforward overview for 10X multiome (GEX+ATAC) analysis includes steps from bioanalyser traces to sequencing and analysis. 

## Bioanalyser tracers
The example tracers and files are all within the repo and they are numbered. There are 5 sample files, and the explanation are numbered respectively.
Bioanalyser traces files are named like so 1.BIO.xxxx (there are 5 in total, therefore 1~5.BIOxxxx) 
### 1
Bioanalyser traces of successful multiome protocol on 30mg mouse cortex (3000 targeted nuclei). The X axis is in secs not bp because lane 7 errored. To get around this you need to manually assign the missing upper/lower marker and then you can switch between bp and secs on the X axis.
### 2 
Bioanalyser traces of successful multiome protocol run through on 4 samples of 30mg mouse cortex(3000 targeted nuclei). ATAC library traces are skewing to the right, however this is not an issue for sequencing. The cDNA showed a good concentration for all as did the RNA library traces. The RNA library is variable between the samples which should not be the case, so the RNA library was redone using leftover cDNA (seen in next bioanalyser trace)
### 3 
Sample 1, 4, 7 are repeats of RNA library generation of 30mg mouse cortex (3000 targeted nuclei). They look a lot more uniform than the previous libraries, although there is a PCR bubble towards the right. The PCR bubble will be omitted in sequencing
### 4 
Bioanalyser traces of multiome protocol on 10mg rotenone/vehicle rat SN (500 targeted nuclei). Traces look alright but very low yield due to low starting sample volume.
### 5
Bioanalyser traces of multiome protocol on 10mg rotenone/vehicle rat SN (9000 targeted nuclei). Looks like transposition didn’t work so ATAc traces only show PCR bubble. cDNA very low so GEX library (RNA library) unlikely to be great.

## Sequencing 
### download to view as it is .html format
The sample sequencing files are all named 1.SEQ.xxxx similar to the bioanalyser files. There are 4 sequencing files numbered 1.SE.xxx to 4.SEQ.xxx. There is also the web summary of cell ranger arc. 

### 1 
QC results
30mg mouse cortex (3000 targeted nuclei)
Original sequencing run of GEX library: Looks ok. Reads don’t map very well to reference, which is expected: R1: Contains cell barcode sequence + 12 bp UMI, which don’t map well to the reference. R2: These should align to the transcriptome. 

### 2 
Rerun of GEX library sequencing: not much change from original run.

### 3
Original sequencing run of ATAC library: Does not look good. Per Sequence GC Content and Sequence Duplication Levels are in particular is concerning. This was due to the dark cycle not being include in sequencing, meaning some adapter sequences were left on the samples during sequencing.

### 4 
Rerun of ATAC library with dark cycles: Looks a lot better. In terms of the reads not aligning well to the reference genome: R1/R3: These should align to the reference, R2: Contains cell barcode sequence, so don't expect these to map well to the reference.

## Download the sequencing data 
Sequenced with BCR: 
BCR provides zipped folders for sequencing runs that could be transferred for ICL’s HPC cluster users using iRODS server. 
Here is a [link](https://imperial-genomics-facility.github.io/igf-pipeline-help/data_access.html#data-access-via-irods-server) for data assess details.
We recommended command line transfer by first setting up the irods server. 

Potential error is that ```mkdir –p ~/.irods``` could fail. In this case, 
1.	Either use ```nano irods``` on HPC home dir 
2.	Type configuration on command line when prompted
You should be able to proceed then. 

BRC will also provide fastQC files of your sequencing, this is data describing the quality of your sequencing run 
Some explanation and examples are included in below session seqencing. 

Note that BRC will likely send you the GEX and ATAC files separately, so you need to wait for both before running cell ranger arc. 


## Using cell ranger arc 
[Cell ranger arc](https://support.10xgenomics.com/single-cell-multiome-atac-gex/software/downloads/latest) must be used for 10X multime GEX + ATAC seq 
We recommend downloading and [installing](https://support.10xgenomics.com/single-cell-multiome-atac-gex/software/pipelines/latest/installation) your own version instead of using the one on lab shared tools. 
Cell ranger arc must be added to your system PATH before using. There are 2 ways to setting it up. 
### 1.	Create a **.bash** script that you reference/ call in when you qsub your **.pbs** script on the cluster 

Example 

```
export PATH=/rds/general/user/xyz16/home/cellranger-arc-2.0.0:$PATH
cellranger-arc count --id=10X_human_sc_multiome_1_output --reference=/rds/general/user/xyz16/home/refdata-cellranger-arc-GRCh38-2020-A-2.0.0 --libraries=/rds/general/user/xyz16/home/human_sc_multiome_1.csv --localcores=16 --localmem=64
```
In this case, your **.pbs** file needs to look like the below, and you can run it on the PBS cluster via ```qsub yourtitle.pbs```. 
```
#PBS -l walltime=24:00:00
#PBS -l select=1:ncpus=16:mem=64gb
module load anaconda3/personal
cd $PBS_O_WORKDIR
bash cell_ranger_arc_bash.sh
```

### 2. Use a .bashrc script 
Create a .bashrc script via Where you add cell ranger arc to your path i.e. see below
![image](https://user-images.githubusercontent.com/53938505/140020226-e680e207-a960-4196-9729-b09acae72870.png)

Once you do this, your system knows to where to look for your cell ranger count all the time. And your **.pbs** file can look like so:
```
#!/bin/bash
#PBS -l walltime=24:00:00
#PBS -l select=1:ncpus=16:mem=64gb
#PBS -j oe
#PBS -o cellranger.log

source ~/.bashrc

cd /rds/general/user/mt2920/home/Multiome_analysis/cellranger-arc
cellranger-arc count --id=reseq_multiome_060921 \
                     --reference=/rds/general/user/mt2920/home/Multiome_analysis/cellranger-arc/referencedata/refdata-cellranger-arc-mm10-2020-A-2.0.0 \
                     --libraries=/rds/general/user/mt2920/home/Multiome_analysis/cellranger-arc/multiome_libraries.csv \
                     --localcores=16 \
                     --localmem=64
```
These are 2 ways to add cell ranger to your PATH and running it. You can pick either 1. 
***Please note the memory and ncpus parameters here are just for reference, this is not and should not be the same for different sequencing runs due to sequencing depth etc. This is something you need to figure out yourself, however the minimum recommandation is 64gb.***

### Web summary of cell ranger -arc 
Web summary: differed from expectation as aimed for 3000 cells but web summary reported 7134 instead, hence ran cellranger -arc count with --min-gex-count=500 --min-atac-count=1000. Warning messages were present but didn’t error so downstream analysis could still be done on the sequenced sample.


