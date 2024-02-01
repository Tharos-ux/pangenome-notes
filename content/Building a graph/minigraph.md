---
title: Minigraph
---
# Graph construction with minigraph

To use [minigraph](https://github.com/lh3/minigraph) on a set of input genomes, with default parameters, you should use this command:

```bash
minigraph -cxggs reference.fasta alt00.fasta alt01.fasta [...] > graph.gfa
```

You can specify any number of `.fasta`/`.fa` files, as well as `.gfa` graph files, and you can find details about arguments [in the manpage](https://lh3.github.io/minigraph/minigraph.html)

+ `c` enables base-level alignment
+ `x` is to specify a preset, here `ggs`, which is a simple algorithm for incremental graph generation

> [!IMPORTANT] Publication and availability
> Publication is [available](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-02168-z), and source code is available [here](https://github.com/lh3/minigraph)

The output will be in **rGFA** format, a sub-type of GFA1 that adds information about positions in the graph but removes information of genomes' origins. In rGFA, you don't have W-lines or P-lines that do serves to get the information of which fragment goes to which genome.
It's a [development choice](https://github.com/lh3/minigraph/issues/27) that was made [in the formalism of rGFA](https://github.com/lh3/minigraph/issues/26), because H. Li see his tool as a way to [embed multiple genomes on a reference](https://github.com/nf-core/pangenome/issues/20), and not doing something which is reference-free.

A pull request was made in 2022, adding [P-lines support to minigraph](https://github.com/lh3/minigraph/pull/77) but was never accepted. However, one can get this version by getting the associated commit ID.

> [!WARNING] Warning
> minigraph outputs nodes prefixed with `s` ; with some tools (such as odgi) it may cause crashes. To convert those rGFA's to standard GFA files, [you can use gfautil](https://github.com/vgteam/vg/issues/3129)

It may be possible to get some kind of paths in a rGFA using `vg convert` according to [this answer](https://github.com/pangenome/odgi/issues/546#issuecomment-1893382366)