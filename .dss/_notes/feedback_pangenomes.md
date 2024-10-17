---
title: Feedback on pangenome graph construction
---
Our object : a 'variation graph' (which is not a De Bruijn graph) which contains nodes with labels.

To construct: 
+ pairwise alignment
	+ with software designed for full-genome alignment
	+ MSA (multiple sequence alignment)
+ graph construction
	+ create nodes and edges
	+ save paths
+ post-process (optionnal)
	+ pruning
	+ `gfaffix` at some point
	+ topological simplification
	+ compression
	+ ...

Used tools today:
+ Variation Graph (VG)
+ Minigraph (MG)
	+ From an alignment `minigraph --ggen -L <min_size_of_variants> -c <genomes>`
	+ The graph is relative to reference: if we can't align on it, we don't put it in graph
	+ L parameter lowered makes minigraph much slower and yield issues
	+ Higher L parameters can help align more diverging sequences
+ Minigraph-Cactus (MGC)
	+ It is possible to give a guide tree
	+ High level SV graph from MG
	+ This graph is used as backbone
	+ Put something as 'reference': this sequence won't be clipped nor cycled
+ PanGenome Graph Builder (PGGB)
	+ Curate data before to disassemble chromosomes (tutorials available, where?)
		+ Huge possibilities: how to cluster chromosomes that are close together?
	+ Use of `wfmash` for pairwise all-vs-all alignment 
	+ For graph induction: `seqwish`
	+ Smoothing with `smoothxg`
		+ May add paths that are not even describing a genome?
		+ Notion of consensus path elaborated [here](https://github.com/pangenome/smoothxg/issues/37)
		+ Keeps a consensus and destroys some paths that does not follow 
		+ From the author:
			+ Many things should be removed
			+ As of now, they don't even use it internally
			+ Output of seqwish: should be default output but very large file
			+ Problem: algorithms like stochastic gradient descent on multi-thread implies that 'seeds' are not fixed: we can have different graphs from the same data 
	![[Pasted image 20240115144532.png]]
	+ Post process with `gfaffix` and `odgi`

Cycles are a problem for future usage of graphs. Implement a tool to 'linearize' a graph?