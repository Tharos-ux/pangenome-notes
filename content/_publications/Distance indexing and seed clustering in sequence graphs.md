URL: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7355256/pdf/btaa446.pdf

Motivation: Graph representations of genomes are capable of expressing more genetic variation and can therefore
better represent a population than standard linear genomes. However, due to the greater complexity of genome
graphs relative to linear genomes, some functions that are trivial on linear genomes become much more difficult in
genome graphs. Calculating distance is one such function that is simple in a linear genome but complicated in a
graph context. In read mapping algorithms such distance calculations are fundamental to determining if seed align-
ments could belong to the same mapping.
Results: We have developed an algorithm for quickly calculating the minimum distance between positions on a se-
quence graph using a minimum distance index. We have also developed an algorithm that uses the distance index
to cluster seeds on a graph. We demonstrate that our implementations of these algorithms are efficient and practical
to use for a new generation of mapping algorithms based upon genome graphs.