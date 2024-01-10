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

## Points of attention

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

> [!WARNING] Warning
> minigraph-cactus is **NOT RECOMMENDED** (see [this discussion](https://github.com/orgs/ComparativeGenomicsToolkit/discussions/1252)) for genomes that have a higher mash distance than 0.02 from the reference; it may [yield a warning](https://github.com/ComparativeGenomicsToolkit/cactus/blob/v2.7.0/src/cactus/refmap/cactus_minigraph.py#L288-L291) but may not do it properly.
> Solutions are :
> + Align with an aligner like **Progressive Cactus** from a tree (`mashtree` can be useful)
> + Cut down sequences to match the threshold
> + Try PGGB

### Control input sequence order

To create graph with sequence in a specific order that you can control, using the argument `minigraphSortInput="none"` disables default sorting by mash distance. It is to be specified in the cactus config file.

### Handling repetitive senquences 

Taken from the minigraph-cactus paper :
> Minigraph-Cactus (in common with all MSA tools we know of[24](https://www.biorxiv.org/content/10.1101/2022.10.06.511217v3.full#ref-24)) cannot presently satisfactorily align highly repetitive sequences like satellite arrays, centromeres and telomeres because they lack sufficiently unique subsequences for minigraph to use as alignment seeds. As such, these regions will remain largely unaligned throughout the pipeline and will make the graph difficult to index and map to by introducing vast amounts of redundant sequence. We recommend clipping them out for most applications and provide the option to do so by removing paths with >N bases that do not align to the underlying SV graph constructed with minigraph (**[Figure 1F](https://www.biorxiv.org/content/10.1101/2022.10.06.511217v3.full#F1)**). In preliminary studies of mapping short reads and calling small variants (see below), we found that even more aggressively filtering the graph helps improve accuracy. For this reason, an optional allele-frequency filter is included to remove nodes of the graph present in fewer than N haplotypes and can be used when making indexes for vg giraffe.

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

## Step-by-step walkthrough

> [!IMPORTANT] Beware
> Reference documentation [can be found here](https://github.com/ComparativeGenomicsToolkit/cactus/blob/master/doc/pangenome.md). Next statements are issued from my own analysis of the pieline. The end-to-end pipeline is accessible [here](https://github.com/ComparativeGenomicsToolkit/cactus/blob/55a5a3f4cc928b646367610ca76bf9b4f42e4769/src/cactus/refmap/cactus_pangenome.py#L316).

At each step, **toil** seems to be called and parameters of the current command are added to it. It may explain why **toil** is lacking some files when each step is executed separately. When exploring a jobstore, it seems files are splitted in three categories : `files`, `jobs` and `stats`.

Best way to run each step of the pipeline individually seems to be starting from the [end-to-end pipeline](https://github.com/ComparativeGenomicsToolkit/cactus/blob/55a5a3f4cc928b646367610ca76bf9b4f42e4769/src/cactus/refmap/cactus_pangenome.py#L316) and doing modifications from it. However, even there, each of the calls is cluttered with **toil** stuff, and so is almost practically not editable to input our own files into it, without re-writing the pipeline from scratch.
### STEP 1 : minigraph
Script [can be found here](https://github.com/ComparativeGenomicsToolkit/cactus/blob/master/src/cactus/refmap/cactus_minigraph.py). It simply builds a minigraph graph within a **toil** pipeline, with a cactus seqfile as input.

Inputs :
+ a set of input fasta

In this first step, sequences are ordered (by default) by their mash distance to the reference. GFA is computed within toil. At the end of this step, the [GFA from minigraph is exported](https://github.com/ComparativeGenomicsToolkit/cactus/blob/55a5a3f4cc928b646367610ca76bf9b4f42e4769/src/cactus/refmap/cactus_minigraph.py#L125C31-L125C31).

Are performed :
+ a loading of the cactus seqfile
+ a verification of sample names, given a few rules :
	+ the "." character is overloaded to specify haplotype
	+ a file is invalid if it starts with "."
	+ if the file ends with a ".", there must be one numeric character behind to specify haplotype 
	+ all sequences names that are prefixed by the reference name are invalid (naming convention for *graphmap-join*)
+ an importation of the sequences from the tree of the seqfile into the **toil** jobstore stored in .fa
+ (if asked so) a [sanitization of the fasta headers](https://github.com/ComparativeGenomicsToolkit/cactus/blob/55a5a3f4cc928b646367610ca76bf9b4f42e4769/src/cactus/preprocessor/checkUniqueHeaders.py#L37)
>	It will strip everything up to and including last # so HG002#0#chr2 would get changed to just chr2 (and then id=HG002| would be prefixed as above) this keeps redundant information out of the sequence names, otherwise it can be duplicated in the final output.  it also keeps # symbols out of sequence names
+ (if asked so) a mash-distance ordering of the input sequences
+ executes minigraph with parameters in the XML file

In a discussion on [cactus discussions](https://github.com/orgs/ComparativeGenomicsToolkit/discussions/1254) it was noted that parallelism of minigraph comes by taking each chromosome on one different thread (as minigraph does not take into account inter-chromosomal events).
### STEP 2A : cactus-graphmap
Script [can be found here](https://github.com/ComparativeGenomicsToolkit/cactus/blob/master/src/cactus/refmap/cactus_graphmap.py). It does the alignment between fasta sequences and the minigraph graph created at step 1.

Inputs :
+ a minigraph graph
+ a set of input fasta

Outputs :
+ PAF file which aligns each fasta to the contig sequences of the graph
+ multifasta containing graph contigs, treated as an assemby by cactus later on

### STEP 2B : cactus graphmap-split

> This script will take the cactus-graphmap output and input and split it up into chromosomes.  This is done after a whole-genome graphmap as we need the whole-genome minigraph alignments to do the chromosome splitting in the first place.  For each chromosome, it will make a PAF, GFA and Seqfile (pointing to chromosome fastas)

### STEP 3 : cactus-align

### STEP 4 : 