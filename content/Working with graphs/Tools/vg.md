---
title: VG toolkit
---
> [!WARNING] Warning
> vg commands on graphs that are compressed **does not work**. It will raise a 'invalid graph type' error.
## Convert from GFA1.1 to GFA1

`vg convert in.gfa -W -f > out.gfa`
+ `-W` stands for suppress W-lines
+ `-f` is to output to file
## Convert from vg, json to GFA
`vg view [-J|-V|-F] input_graph -g > out.gfa`

## Call bubbles on graph to get variants
`vg deconstruct -p ref graph.gfa > variants.vcf`
+ `-p [STR]` stands for the path to use as reference to call variants