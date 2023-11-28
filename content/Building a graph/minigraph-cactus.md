---
title: Minigraph-cactus
---
# Graph construction with minigraph-cactus

> [!IMPORTANT] Beware
> Many parameters are controlled by the catus config file, and so you need to have [a copy of it](https://github.com/ComparativeGenomicsToolkit/cactus/blob/master/src/cactus/cactus_progressive_config.xml) that you edit and use the flag `--configFile my-config.xml` in the dedicated commands.

Here are some timings and disk consumption:

| Data origin | Description           | $\sum_{size}$ | \|genomes\| | $\frac{time}{cores}$ | peak memory | $G_{size}$ | $Folder_{size}$ |
|-------------|-----------------------|---------------|-------------|----------------------|-------------|------------|-----------------|
| Yeast       | Chromosome 1 (200kb)  | 3.2MB         | 15          | 1.125 min            | 21Go        | 3.8MB      | 10MB            |
| Maize       | Part of chr. 6 (20Mb) | 60MB          | 4           | 2.625 min            | 53Go        | 61.9MB     | 150MB           |
| Bovine      | Chromosome 28 (47Mb)  | 136MB         | 3           | 1.375 min            | 43Go        | 94MB       | 210MB           |
| Bovine      | Chromosome 3 (210Mb)  | 620MB         | 3           | 2.625 min            | 71Go        | 280MB      | 550MB           |

## Step-by-step walkthrough

> [!WARNING] Warning
> minigraph-cactus uses **toil**, a [pipeline managment system](https://toil.ucsc-cgl.org/) that stores its temporary files in a folder. As of now, if you run step-by-step the pipeline, you won't have any issues, but if you try to recreate on your own one of those steps, or re-run a previously run step, you will encounter errors. Tje safest practice for a standard usecase is to destroy the jobstore manually once the job is finished.

### Files preparation

Fasta files must be separated, sample by sample.
You need to prepare a `\t`-separated file, with sequence names and sequence paths (better if absolute).

Example (taken from minigraph-cactus documentation):

```
# Diploid sample:
HG002.1  ./HG002.paternal.fa
HG002.2  ./HG002.maternal.fa

# Haploid sample:
CHM13  ./chm13.fa
```

Lines with a `#` are interpreted as notes, and will be skipped. Empty lines will also be skipped.

> [!WARNING] Warning
> Strictly don't use `_` in sequence names, nor spaces, nor prefixes (ex: if you have a sequence named `zea252` and a sequence named `zea2`, pipeline will crash. `seq1` and `seq10` also, but not `seq01` and `seq00`)

### Choosing a reference

The reference will satisfy the following properties:
+ Never be clipped.
+ It's path in the output graph will be acyclic
+ Only visit nodes in their forward orientations
+ Be a "reference-sense" path in vg/gbz and will therefore be indexably for fast coordinate lookup
+ Be the basis for the output VCF and therefore won't appear as a sample in the VCF
+ Be used to divide the graph into chromosomes
One can define multiple references, but it won't help for clipping (but for filter?), cyclicity, nor nodes in forward orientation purposes.

### Control input sequence order

To create graph with sequence in a specific order that you can control, using the argument `minigraphSortInput="none"` disables default sorting by mash distance. It is to be specified in the cactus config file.


### Control graph output filtering

> [!WARNING] Warning
> By default, a minigraph-cactus graph does not feature full sequences from the input genome set. Depending on the application, it might hurt downstream analysis, so carefully choose your **filter** and **clip** values in order wto get the graph you want in return!

As of latest versions, `--gfa full clip filter` raises an error. Using instead `--clip 0 --filter 0` on step `cactus-graphmap-join` seems to fix the issue, issuing the `full (.full)` graph. Following the same logic :
+ `--clip 10000 --filter 0` gives the default `clip` graph (default issued by the one-liner pipeline)
+ `--clip 10000 --filter 2` gives the `filter (.d2)` graph.
Those values can also be specified in the cactus parameters file.

## General commands

```bash
# $1 -> path to .txt file describing where fasta files are
# $2 -> output path and name for the final graph (without extension)
# $3 -> name of reference sequence inside .txt file
# $4 -> a temporary name for a temporary folder

# destroy any .jobstore
[ -d ./jobstore_$4 ] && rm -r ./jobstore_$4
cactus-minigraph ./jobstore_$4 $1 $2.gfa --reference $3
cactus-graphmap ./jobstore_$4 $1 $2.gfa $2.paf  --reference $3 --outputFasta $2.sv.gfa.fa.gz
cactus-align ./jobstore_$4 $1 $2.paf $2.hal --pangenome --outGFA full clip filter --outVG --reference $3
cactus-graphmap-join ./jobstore_$4 --vg $2.vg --outDir ./$2 --outName $4 --reference $3 --clip 0 --filter 0
```

Details about steps:
+ `cactus-minigraph` command creates a minigraph with base-level alignment graph by default. It can be change by altering the parameter `minigraphConstructOptions="-c -xggs"` in the cactus config file, accordingly to the [minigraph documentation](https://github.com/lh3/minigraph).
+ It is possible to get a partial graph at the `cactus-align` step, with the flag `--outGFA`
+ With the flag `--gfa full clip filter` (when available), the full graph happen to have many paths labeled `_MINIGRAPH_` whose are decomposition of minigraph nodes.

Pipeline can also be executed in a single command:
```bash
cactus-pangenome <jobStorePath> <seqFile> --outDir <output directory> --outName <output file prefix> --reference <reference sample name>
```