---
title: GfaPy
---
> [!WARNING] Warning
> This libraray is written in pure Python, and features recursion. On huge graphs, it may fail. A temporary hack consists on raising the maximum recursion depth of the current Python interpreter with `sys.setrecursionlimit()`, but only delays the issue.