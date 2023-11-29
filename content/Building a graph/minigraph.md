---
title: Minigraph
---


To use [minigraph](https://github.com/lh3/minigraph) on a set of input genomes, with default parameters, you should use this command:

```bash
minigraph -cxggs reference.fasta alt00.fasta alt01.fasta [...] > graph.gfa
```

You can specify any number of `.fasta`/`.fa` files, as well as `.gfa` graph files, and you can find details about arguments [in the manpage](https://lh3.github.io/minigraph/minigraph.html)

+ `c` enables base-level alignment
+ `x` is to specify a preset, here `ggs`, which is a simple algorithm for incremental graph generation

The output will be in **rGFA** format, a sub-type of GFA1 that adds information about positions in the graph but removes information of genomes' origins. In rGFA, you don't have W-lines or P-lines that do serves to get the information of which fragment goes to which genome.
It's a development choice

> [!WARNING] Warning
> minigraph outputs nodes prefixed with `s` ; with some tools (such as odgi) it may cause crashes.