Please see 09282026 nhaA_dnds.md for previous attempts.

As stated previously, I ended up going with option b) a completely different approach. Of the four aligners I trialed (mafft, translatorx, clustalo, and macse), only the codon-aware macse seemed to run properly. However, the process took way too long.
Therefore, my new goal was to make a quicker "codon-aware" aligner that deletes any pseudogenomes with random stop codons in the middle of the sequence.
Please see [Here](https://www.reddit.com/r/bioinformatics/comments/7gcr9k/a_good_tool_to_perform_codon_aware_dna_alignments/) for what I used for inspiraton.

```
import subprocess
from Bio import Blast, SeqIO
from Bio.SeqRecord import SeqRecord

subprocess.run(["blastn", "-query", "/home/bahar/danssaltgenes/nhaA_ref.ffn", "-db", "/home/bahar/danssaltgenes/geneDatabase", "-perc_identity", "70", "-qcov_hsp_perc", "70", "-outfmt", "5", "-max_target_seqs", "10000", "-num_threads", "8", "-out", "/home/bahar/danssaltgenes/nhaA_hits.xml",])

allMatches = Blast.read("/home/bahar/danssaltgenes/nhaA_hits.xml")
bestHit = {}
for hit in allMatches:
  valueGene = hit.target.description
  keyGenome = valueGene.split("__")[0]
  if keyGenome not in bestHit:
    bestHit[keyGenome] = valueGene

topHitNames = set(bestHit.values())
proteinSeqs = []
dnaSeqs = []
for gene in SeqIO.parse("/home/bahar/danssaltgenes/allGenes.ffn", "fasta"):
  if gene.id not in topHitNames:
    continue
  if len(gene.seq) % 3 != 0:
    continue
  protein = gene.seq.translate()
  if protein.endswith("*"):
    protein = protein[:-1]
    gene.seq = gene.seq[:-3]
  if "*" in protein:
    continue
  proteinSeqs.append(SeqRecord(protein, id=gene.id, description=""))
  dnaSeqs.append(SeqRecord(gene.seq, id=gene.id, description=""))

SeqIO.write(proteinSeqs, "/home/bahar/danssaltgenes/nhaA_AA.faa", "fasta")
SeqIO.write(dnaSeqs, "/home/bahar/danssaltgenes/nhaA_DNA.ffn", "fasta")

subprocess.run(["mafft", "--auto", "/home/bahar/danssaltgenes/nhaA_AA.faa"], stdout=open("/home/bahar/danssaltgenes/nhaA_AA_Aligned.faa", "w"),)

subprocess.run(["pal2nal.pl", "/home/bahar/danssaltgenes/nhaA_AA_Aligned.faa", "/home/bahar/danssaltgenes/nhaA_DNA.ffn", "-output", "fasta"], stdout=open("/home/bahar/danssaltgenes/nhaA_DNA_Aligned.fasta", "w"),)

subprocess.run(["FastTree", "-nt", "-gtr", "/home/bahar/danssaltgenes/nhaA_DNA_Aligned.fasta"], stdout=open("/home/bahar/danssaltgenes/nhaA_tree.nwk", "w"),)

subprocess.run(["hyphy", "fel", "--alignment", "/home/bahar/danssaltgenes/nhaA_DNA_Aligned.fasta",  "--tree", "/home/bahar/danssaltgenes/nhaA_tree.nwk",])
```

Please see dataoutput [Here](https://docs.google.com/spreadsheets/d/1slWeLV5dUJYiZ5u1HiTtpBAhgL5gPMxEpjFpUIKkMIE/edit?usp=sharing)
