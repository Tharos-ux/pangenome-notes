---
title: "PGGB : PanGenome Graph Builder"
---
# Graph construction with PGGB

## Step-by-step walkthrough

### Files preparation

> [!NOTE]\
> Fasta must be mereged in a single file, for instance using `cat *.fasta > out.fa`. Then, it needs to be indexed with `samtools faidx out.fa`. Just specify the main `out.fa` file when using PGGB, it will find the indexed file by itself.

### PGGB output

The pipeline outputs six different graphs, that corresponds to different steps of the pipeline. 