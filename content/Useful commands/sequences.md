---
title: Interact with sequences
---
Get statistics on sequences:
+ awk command to get the size of all lectures in a file : `awk '/^>/{if (l!="") print l; print; l=0; next}{l+=length($0)}END{print l}' unique.fasta |paste - -`
+ samtools command to index a file (useful for pggb): `samtools faidx myfile.fasta`