---
title: "PGGB : PanGenome Graph Builder"
---
# Graph construction with PGGB

### Files preparation

> [!NOTE] Note
> Fasta must be mereged in a single file, for instance using `cat *.fasta > out.fa`. Then, it needs to be indexed with `samtools faidx out.fa`. Just specify the main `out.fa` file when using PGGB, it will find the indexed file by itself.

### Build graph from multifasta files
In the case you build from multiple individuals (files) with many entries (fasta fields), each . You will find reference to the files in the P-lines:
+ the name given to the sample (cactus pipeline file) will be **in the first field**.
+ the name of each fasta header will be **in the third field**.
### PGGB output

The pipeline outputs six different graphs, that corresponds to different steps of the pipeline. 