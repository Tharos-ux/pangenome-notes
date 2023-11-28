---
title: BubbleGun
---
Tool for detecting bubbles and superbubbles in De-bruijn/variation graphs. Several graph-related functions are also implemented in BubbleGun:
- Graph compacting (merging linear stretches of single nodes),
- Extracting the biggest connected component in the graph
- Separating certain chains by their id for further examination
- Extracting a user-specified neighborhood size around a node to extract as a separate graph for examination
- Extracting two random paths from each bubble chain for haplotyping
- Extracting information from long reads aligned to bubble chains

Publication available [here](https://academic.oup.com/bioinformatics/article/38/17/4217/6633304), source code available [here](https://github.com/fawaz-dabbaghieh/bubble_gun).

> [!WARNING] Warning
> The function `bfs` in the package starts an infinite loop if target node is on a end of the graph.