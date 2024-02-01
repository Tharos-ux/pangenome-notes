---
title: "GfaGraphs: Python abstraction layer for GFA graph format"
---
![[library_flowchart.png]]
Known limitations:
+ As of now, not scaling well in terms of memory for huge graphs (like full HPRC) as 256G of RAM is not sufficient to load PGGB and MGC graphs in memory at the same time
+ Takes a long time to load huge graphs (many hours for HPRC aswell)