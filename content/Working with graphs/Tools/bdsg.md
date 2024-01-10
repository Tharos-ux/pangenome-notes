---
title: LIBBDSG
---
The software libbdsg is both:
+ A C++ library (libbdsg)
+ Python bindings (bdsg)
Installing with `pip` only installs python bindings, while installing with `cmake` grants the full library and the bindings.

> [!IMPORTANT] Publication and availability
> Publication available [here](https://pubmed.ncbi.nlm.nih.gov/33040146/), source code available [here](https://github.com/vgteam/libbdsg).
# Installation with `pip` (Python bindings)

> [!WARNING] Warning
> In order to install `bdsg`, `cmake` and `jansson-devel` are required.

You may encounter installation issues with this package when you try to install it with `pip`. If you [happen to be in such a place](https://github.com/vgteam/libbdsg/issues/181), you may need to edit something in the source code before installing:

```bash
git clone --recurse-submodules git@github.com:vgteam/libbdsg.git
cd libbdsg
nano libbdsg/bdsg/deps/libhandlegraph/src/include/handlegraph/trivially_serializable.hpp
# Add '#include <cstdint>'
python -m pip install . --use-pep517 # pep517 required to trigger installation with .toml file
```