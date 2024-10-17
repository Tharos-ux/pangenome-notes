---
title: VG toolkit
---
> [!WARNING] Warning
> vg commands on graphs that are compressed (.gz files) **does not work**. It will raise a 'invalid graph type' error.

## Convert rGFA to somewhat of a GFA1
The command `vg convert -Wf -r 89 graph_rgfa.gfa > graph_gfa1.gfa` adds the variations and the reference as paths in the graphs. Those paths __**do not describe genomes**__ as rGFA is not a container that keeps this information (erases SNPs and lose paths), but it can help to have a compatible GFA1 file.
## Convert from GFA1.1 to GFA1

`vg convert in.gfa -W -f > out.gfa`
+ `-W` stands for suppress W-lines
+ `-f` is to output to file
## Convert from vg, json to GFA
`vg view [-J|-V|-F] input_graph -g > out.gfa`

## Call bubbles on graph to get variants
`vg deconstruct -e -a -p ref graph.gfa > variants.vcf`
+ `-p [STR]` stands for the path to use as reference to call variants
+ for W-lines, paths are referenced with two parts : link those with a '#' sign.
Generally, post-treatment is done on those vcf files, using **vcfbub** and **vcfwave** 
```
# For some reason input must be gzipped, gzip file.vcf
# The VCF shall also contain snarl level annotations
vcfbub -l 0 -r 10000 -i pggb.vcf.gz >filt_pggb.vcf
# vcfwave requires the GT filed to be removed
awk -i inplace '{$0=gensub(/\s*\S+/,"",3)}1' file
```