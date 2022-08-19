# CRISPRi-dual-sgRNA-screens
## 1. sgRNA counts 
This part contains scripts for alignment of sequencing data from dual-sgRNA CRISPR screening data. These scripts were adapted from: https://github.com/mhorlbeck/ScreenProcessing

For alignment of data without UMI, use python `dualguide_fastqgz_to_counts.py`. Example command:

```bash
python dualguide_fastqgz_to_counts.py 20200513_library_1_2_unbalanced.csv  alignments fastq/UDP0007* fastq/UDP0006*
```

Here, fastq/UDP0007* and fastq/UDP0006* are fastq files (the script will automatically detect read 1 and read 2; you can add as many fastq files as you want); "alignments" is the output directory; 20200513_library_1_2_unbalanced.csv is the library file. The output counts file can then be input into MAGECK or analyzed directly.


For libraries with UMI, demultiplexing on only the i5 index using the i7 index (IBC) as a read is performed as detailed: https://gist.github.com/sumeetg23/a064a36801d2763e94da2e191699fb9f. For alignment of data with UMI, use python dualguide_UMI_fastqgz_to_counts.py. Example command: 

```bash
python dualguide_UMI_fastqgz_to_counts.py 20200513_library_1_2_unbalanced.csv \
  UMI_sequences_filtered.csv \
  alignments \
  JR72_S1_L001* JR72_S1_L002* JR72_S1_L003* JR72_S1_L004*
```

## 2. Merge counts for multiple samples  
This part will merge outputs into a table in which rows are each sgRNA-UMI combination and columns are sample names. 

[//]: # (From this, we can then calculate the enrichment of each guide between screen arms in python or MaGECK.)
```bash
# to-do
```

## 3. Calculate the enrichment of each guide between screen arms
Sample sheet example: 
```
Index,Treat,Rep
i05,T0,2
i06,T0,1
i07,DMSO,1
i08,DMSO,2
i09,DMSO,3
i10,RM6,1
i11,RM6,2
i12,RM6,3 
```

Command line script example: 
```bash
Rscript PhenotypeScores.R \
-i demo/counts.txt -s demo/samplesheet.txt \
-t RM6 -c DMSO \
-o demo
```

Output:
```
sgRNA1  sgRNA2  gene    baseMean        log2FoldChange  lfcSE   stat    pvalue  padj
ATXN7L1_+_105516927.23-P1P2|ATXN7L1_+_105516907.23-P1P2++ATXN7L1_+_105516927.23-P1P2|ATXN7L1_+_105516907.23-P1P2        ATXN7L1_+_105516927.23-P1P2|ATXN7L1_+_105516907.23-P1P2        ATXN7L1_+_105516927.23-P1P2|ATXN7L1_+_105516907.23-P1P2 ATXN7L1 340.822823088435       0.287402827440405       0.173372082809046       2.84433175219573        0.241191060818789       0.466352832143249
```