---
title: GFAffix
---
Tool is referenced [here](https://github.com/marschall-lab/GFAffix)

To run GFAffix, the command is: `gfaffix <input_gfa> -o <output_gfa>`.

> [!NOTE] Note
> The last step of [[pggb]] applies GFAffix (taken from the docs: "Finally, we apply gfaffix to remove forks where both alternatives have the same sequence.") and [[minigraph-cactus]] applies it in it's last step (`cactus-graphmap-join`); however, if applying GFAffix on a PGGB graph returns the same graph, it is not the case for minigraph-cactus. We can expect that GFAffix is not the last step of `cactus-graphmap-join`, or is ran with exclusion patterns.

# GFAffix and [[editions]]

From the definition of [[editions]], I wanted to see how GFAffix impacted the resulting graph and the distance to other graphs. Without any surprise as the tool is present in both pipelines, the impact of running GFAffix is marginal.

![[gfaffix_clustering.png]]

