---
title: bGFA - bubble Graphical Fragment Assembly
---
GFA is, as time of writing, the current standard to share variation graphs. Human-readable and used by many software, featuring tab-separated fields that contain graph information, it however lacks two crucial things:
+ this file format has no random access built-in.
+ this file format can feature billions of nodes, and thus may be hard to visualize.
This work tries to address the second problematic. I want here to define another sub-standard of GFA, which is the bGFA. bGFA consists of a fully-compatible GFA1.0, 1.1 or even rGFA with at its end some B-lines. B-lines will follow the GFAspec format, and allows for this syntax:

```
H	VN:Z:1.1	RS:Z:seq0
S	1	ATTA
...
S	12	GGTC
W	seq1	1	seq1	0	749948	>1>2>4>5>6>7>8>9>10>11>9>10>11>12
W	seq2	2	seq2	0	748216	>1>3>4>5>7>9>10>12
W	seq0	0	seq0	0	749379	>1>2>4>5>6>7>9>10>11>12
L	1	+	2	+	0M
...
L	11	+	12	+	0M
B	b0	4,1,2,3
B	b1	7,5,6
```

Each B-line describes a bubble in the graph, and gives the node names it contains for easy access and isolation. Key idea (not implemented yet) is to have a recusive definition, where bubbles can be composed of nodes and bubbles, embedding a hierarchy which would allow for different levels of abstraction over 

Other thoughts I had when thinking about this format are:
+ define another type of record, the **bubble chain**, which describes the juxtaposition of at least two bubbles
+ precompute some values as optional fields such as starting and ending points of the bubble