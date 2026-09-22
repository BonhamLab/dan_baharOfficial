Selection in among salt tolerance genes across Bifidobacterium infantis genomes
The goal is to find evidence of natural selection (either positive or stabalizing selection) among 20 salt-tolerance genes across 4000+ Bifidobacterium infantis genomes. We have to find out how the genomes relate, where the genes sit, whether they are always present, and whether there exists variations among mutations.
dN/dS is an important concept. Selection is measured by the ratio of non-synonymous to synonymous substitutions in a gene. dN/dS greater than 1 suggests positive/diversifying selection, about 1 is neutral, and less than 1 is conservative/stabalizigin selection. I originally thought that we could potentially be more efficiency with a dN/dS calculation by using the protein sequences rather than the entire DNA strands, but we won’t actually know if there is a synonymous mutation occurring if two codons code for the same protein. 
First, we gotta familiarize ourselves with the genomes: the genomes live within dogen (path: lab/binfanctious/genomes/data/bilgenome, a folder of FASTA files), and a results folder holds annotated proteins (GFF files). I’ll choose around 10 genes to look at and see how they are labeled and what is within each of their folders
Second, we gotta generate proper alignments: For each target gene, gather its version (ortholog) from every genome, align them, and eyeball the alignment to make sure it is clean. 
Multiple Sequence Alingment (MSA) = all sequences aligned to each other simultaneously
Tools: Clustal Omega
Third, we gotta do an analysis of dN/dS
On each codon-aware alignment, see their dN/dS scores to see which salt genes are under selection.
Tools: HyPhy
At some point, we will scale up via NextFlow
