### This is Step 1 within the larger nhaA analysis pipeline. The code below creates the gene database from BLAST by combining every gene from every genome into one file, allGenes.ffn 

This is Step 1 of the msa workflow: Creating the cluster database
```
import os
from Bio import SeqIO
import subprocess

genomes = os.listdir("/lab/binfantis/genomes/results")

allGenes = []
for genome in genomes:
    geneFile = "/lab/binfantis/genomes/results/" + genome + "/" + genome + ".ffn"
    for gene in SeqIO.parse(geneFile, "fasta"):
        gene.id = genome + "__" + gene.id
        gene.description = ""
        allGenes.append(gene)

SeqIO.write(allGenes, "/home/bahar/danssaltgenes/allGenes.ffn", "fasta")
```
