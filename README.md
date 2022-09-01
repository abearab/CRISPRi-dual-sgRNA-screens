# CRISPRi-dual-sgRNA-screens
## sgRNA counts 
This part contains scripts for alignment of sequencing data from dual-sgRNA CRISPR screening data. These scripts were adapted from: https://github.com/mhorlbeck/ScreenProcessing

For alignment of data without UMI, use python `dualguide_fastqgz_to_counts.py`. Example command:

```bash
python module1/dualguide_fastqgz_to_counts.py \
  20200513_library_1_2_unbalanced.csv  \
  alignments \
  fastq/UDP0007* fastq/UDP0006*
```

Here, fastq/UDP0007* and fastq/UDP0006* are fastq files (the script will automatically detect read 1 and read 2; you can add as many fastq files as you want); "alignments" is the output directory; 20200513_library_1_2_unbalanced.csv is the library file. The output counts file can then be input into MAGECK or analyzed directly.


For libraries with UMI, demultiplexing on only the i5 index using the i7 index (IBC) as a read is performed as detailed: https://gist.github.com/sumeetg23/a064a36801d2763e94da2e191699fb9f. For alignment of data with UMI, use python dualguide_UMI_fastqgz_to_counts.py. Example command: 

```bash
python module1/dualguide_UMI_fastqgz_to_counts.py 20200513_library_1_2_unbalanced.csv \
  UMI_sequences_filtered.csv \
  alignments \
  JR72_S1_L001* JR72_S1_L002* JR72_S1_L003* JR72_S1_L004*
```

## Merge counts for multiple samples  
This part will merge outputs into a table in which rows are each sgRNA-UMI combination and columns are sample names. 

[//]: # (From this, we can then calculate the enrichment of each guide between screen arms in python or MaGECK.)
```bash
python module1/count_files_to_counts_matrix.py alignments results
```
### Output 
Raw counts matrix (`counts.txt`) 
```
columns: sample IDs
rows: sgID_AB counts
```

## Calculate the enrichment of each guide between screen arms
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
Rscript module2/PhenotypeScores.R \
  -i results/counts.txt \
  -s results/samplesheet.txt \
  -t RM6 -c DMSO
```

### Outputs
These outputs will be written in the same folder as the input count matrix.
1. Normalized counts (`counts_DESeq2_normalized.txt`): 
```
columns: sample IDs
rows: sgID_AB counts (DESeq2 normalized)
```
2. Phenotype scores (`{phenotype}{cond1}_vs_{cond2}.txt`)

```
    target  baseMean        log2FoldChange  lfcSE   stat    pvalue  padj
MAP1LC3B_-_87425948.23-P1P2|MAP1LC3B_-_87425925.23-P1P2++MAP1LC3B_-_87425948.23-P1P2|MAP1LC3B_-_87425925.23-P1P2        MAP1LC3B       598.454899564438        0.0345856896391499      0.14241765524649        0.502626099621637       0.777778849921918     0.88940640596369
```

