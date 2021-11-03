# 10X_multiome_anlysis
Simple straightforward overview for 10X multiome (GEX+ATAC) analysis includes steps from bioanalyser traces to sequencing and analysis. 

## Bioanalyser tracers
### The example tracers and files are all within the repo and they are numbered. There are 5 sample files, and the explanation are numbered respectively. 
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
