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

### Web summary of cell ranger -arc 
Web summary: differed from expectation as aimed for 3000 cells but web summary reported 7134 instead, hence ran cellranger -arc count with --min-gex-count=500 --min-atac-count=1000. Warning messages were present but didn’t error so downstream analysis could still be done on the sequenced sample.
