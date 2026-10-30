```
import subprocess
from Bio import Blast, SeqIO
from Bio.SeqRecord import SeqRecord

subprocess.run([
    "blastn",
    "-query", "/home/bahar/danssaltgenes/nhaA_ref.ffn",
    "-db", "/home/bahar/danssaltgenes/geneDatabase",
    "-perc_identity", "80",
    "-qcov_hsp_perc", "90",
    "-outfmt", "5",
    "-max_target_seqs", "10000",
    "-num_threads", "8",
    "-out", "/home/bahar/danssaltgenes/nhaA_hits_direct.xml",
])

allMatches = Blast.read("/home/bahar/danssaltgenes/nhaA_hits_direct.xml")
bestHit = {}
for hit in allMatches:
    valueGene = hit.target.description
    keyGenome = valueGene.split("__")[0]
    if keyGenome not in bestHit:
        bestHit[keyGenome] = valueGene

topHitNames = set(bestHit.values())
dnaSeqs = []
for gene in SeqIO.parse("/home/bahar/danssaltgenes/allGenes.ffn", "fasta"):
    if gene.id not in topHitNames:
        continue
    if len(gene.seq) % 3 != 0:
        continue
    if gene.seq.translate().endswith("*"):
        gene.seq = gene.seq[:-3]
    if "*" in gene.seq.translate():
        continue
    dnaSeqs.append(SeqRecord(gene.seq, id=gene.id, description=""))

dnaSeqs = dnaSeqs[1:500] + dnaSeqs[625:]
dnaSeqs = [s for s in dnaSeqs if s.id != "2503754032__IGEEMI_00742"]
SeqIO.write(dnaSeqs, "/home/bahar/danssaltgenes/nhaA_DNA_direct.ffn", "fasta")

subprocess.run(
    ["mafft", "--auto", "/home/bahar/danssaltgenes/nhaA_DNA_direct.ffn"],
    stdout=open("/home/bahar/danssaltgenes/nhaA_DNA_direct_Aligned.fasta", "w"),
)

subprocess.run(
    ["FastTree", "-nt", "-gtr", "/home/bahar/danssaltgenes/nhaA_DNA_direct_Aligned.fasta"],
    stdout=open("/home/bahar/danssaltgenes/nhaA_tree_direct.nwk", "w"),
)

subprocess.run([
    "hyphy", "fel",
    "--alignment", "/home/bahar/danssaltgenes/nhaA_DNA_direct_Aligned.fasta",
    "--tree", "/home/bahar/danssaltgenes/nhaA_tree_direct.nwk",
])
```
