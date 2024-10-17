---
title: Interact with sequences
---
Get statistics on sequences:
+ awk command to get the size of all lectures in a file : `awk '/^>/{if (l!="") print l; print; l=0; next}{l+=length($0)}END{print l}' unique.fasta |paste - -`
+ samtools command to index a file (useful for pggb): `samtools faidx myfile.fasta`
+ Replace string in file: `sed -i "s/thing_to_replace/thing_replacing/g" file > out` and replace spaces `sed 's/[[:space:]]/_/g' file.fa > out.fa`
+ Replace within file: `sed -i 's/>chr7/>newname/g' fasta.fa`
Split a multifasta file:
+ `awk -F '>' '/^>/ {F=sprintf("%s.fasta", $2); print > F;next;} {print F; close(F)}' < file.fasta`

Get fast stats on a GFA file:
+ Print all number of lines types: `<graph.gfa sed 's/^\(.\).*/\1/' | sort | uniq -c` 
+ Get all P-lines from file: `grep '^P' input_file.gfa | awk '{print $2}'`
+ Get global graph size: `grep '^S' mgc_human_1.gfa | awk '{sum+=length($3)} END{print sum}'`

Extract subparts of a VCF file : `grep '^#[^#]\|^chr1[[:blank:]]' file

Schedule jobs on SLURM cluster:
+ See [here](https://stackoverflow.com/questions/60583279/how-to-make-sbatch-job-run-after-a-previous-one-has-completed) to chain jobs

Extract entries from multifasta file:
- samtools faidx (with [examples](https://www.biostars.org/p/49820/))