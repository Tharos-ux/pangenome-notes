---
title: odgi
---
List of [commands](https://odgi.readthedocs.io/en/latest/rst/commands/odgi.html) is available here.
# General commands
## Convert graph formats

To convert from odgi (.og) format to another formats (like GFA for instance) it is possible to use `odgi view`.

```bash
# Convert to GFA
odgi view -g -i $INPUT > $OUTPUT
# INPUT is a .og file
# OUTPUT is a new .gfa file
# -g stands for "convert to gfa"
```

To convert from GFA to odgi, you can use `odgi build`
## Draw a static render of a graph

To create a static (.png) representation of a graph, two steps are required. The first one is to layout the graph with `odgi layout` to get a layout file (where to position nodes in the static file) than using `odgi draw` to render the final picture.
First and foremost, before the layout computation, the graph has to be optimized. Interestingly, on graphs from 42 T2T yeast genomes, the graph from [[minigraph-cactus]] was optimized but not the one from [[pggb]].

```bash
# You can convert the graph in .og format to speed up the process
odgi build -g graph.gfa -o graph.og
# If needed, optimize the graph
odgi sort -i graph.og -O -o graph_opti.og
# Create the layout
odgi layout -i graph.og -o layout.lay
# Draw the final figure

```


# Python bindings

> [!WARNING] Warning
> It exists an older implementation of bindings, which is the one referenced in the readsthedocs.io, HOWEVER it is not [the one which should be used](https://github.com/pangenome/odgi/blob/master/test/python/odgi_ffi.md) for [performance reasons](https://github.com/pangenome/odgi/blob/master/test/python/odgi_performance.md) as well as stability issues...

According to the documentation, `odgi_ffi` is meant to be used more as a tool to build a Python library than being the actual Python library.

> Note that odgi also has an older high-level Python API `import odgi` that is somewhat obsolete. Instead you should probably use below `import odgi_ffi` lower level API to construct your own library.

In order to fix segfaults, set `LD_PRELOAD=libjemalloc.so.2` before running Python scripts. However, I could not get it to work in any way, as if I can as the time I'm writing those lines import the bindings, I could not load a graph, giving an error when I try to (`RuntimeError: Error rewinding to load non-magic-prefixed SerializableHandleGraph`) . Given the maintainers are not implying that the [library is not fully stable](https://github.com/pangenome/odgi/issues/425#issuecomment-1305566300) I won't settle on it for the future.