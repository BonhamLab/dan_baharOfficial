# STEP 0: Ensure that you are within msa_env

# STEP 1: Make txt with the first ten genomes
```
mkdir -p ~/danssaltgenes
cd ~/danssaltgenes
ls /lab/binfantis/genomes/results/ | head -10 > ~/danssaltgenes/tentestgenomes.txt
cat ~/danssaltgenes/tentestgenomes.txt
```
# STEP 2: Give dogen the reference nhaA sequence, found via blast
```
cat > ~/danssaltgenes/nhaA_ref.ffn << 'EOF'
>nhaA_ATCC15697
ATGGCCACGACCGCCGGCGCGAAGAAGGGACTTTGGTCCACCATCAGGCGAATCGCCGCCTCAGACCGTATTTCCGGACTCATCATGCTCGGCTTCGCGCTCACCGGCCTGGTGCTCGCGAACCTGCCCGCCACCGCGCATGCGTTCGAGACCGTAGCGGAAACGCATCTGTTCATCCCCTACACCAATCTCGACCTGCCGATTGGCCATTGGGCCCAGGATGGTCTGCTTACCATCTTCTTCCTGACCGTGGGCCTCGAACTGAAGCAGGAACTCACCACCGGCTCGCTCGCCAATCCGAAAGCGGCCGCCGTGCCGATGCTGTGCGCGGTGGGCGGCATGATCGCCCCGCCCATCCTGTTCCTGGCCGTCACGGCATTGTTCTCTCAGATCGGGCCTGGTGAACCCGGCACGCTGATTCTGACCACCACCGGCAGCAGCATCCCGTTCTCGGAGATGTCGCACGGCTGGGCAGTGCCCACCGCCACGGATATCGCGTTCTCACTGGCCGTGCTCGCCCTGTTTGCCAAGGCCCTACCCGGTTCGATCCGCGCGTTCCTGATGACGCTTGCCACCGTCGATGACCTGCTGGCCATTATTTTGATCGCCGTATTCTTCTCCTCGATCAACGCCTGGTATTGGTTCATCGGCATCGTCGTGTGCGCCGTCATCTGGGCATACCTCGTACGACTGAAAAAAGTGCCGTGGATCGCGGTCGGCATCGTCGGCGTCCTCGCTTGGATCATGATGTTCGAGGCAGGCGTGCATCCCACACTGGCCGGCGTGCTGGTTGGCCTGCTGACCCCCTCCCGTGAAATGCACGGCGAACTTTCACCGCGTGCCGAACGCTATGCCAACAAGCTTCAGCCATTCTCCGCACTGCTGGCCCTGCCCATCTTCGCCCTGTTCGCCACCGGCGTGCACTTCGAGTCGGTGAGCCCGCTGTTGTTGGCTTCGCCGTTGGTCATTGCACTGATTGTGGCTTTGGTGGTCGGCAAACCTCTGGGCATCATCACCACCGCATGGCTGGCCACGCATGTGGGCGGGCTCAAGATGGCCAAGGGGCTGCGCGTGCGCGACATGATTCCCGCTGCCGTCGCCTGCGGCATTGGCTTCACTGTGTCCTTCCTCATCGCCTCGCTGGCGTACAAGAACGCGGAACTTTCCGCTGAGGCGCGCTTCGGCGTGCTGGTGGCCTCCCTGATCGCCGCCGCGATTTCCGGTGTGCTGCTGAGCCGTCAGTCCAAGAGATTCGAGAAGGCCGCAGCCGCGCAGGCTGCCGCCGCCGAGGCGTTGGCCGATGCCGAAGCCGGCGAATCGATTGATGGCAATGGCACCGGGCAGCCAAGCCGCACCACAAAGCCCACCACGCCCACGGAACACCCCGGCACCCTCGCCGACGGCACCGCCAGTGTGGAAATCGACTTCCGCCACTGA
EOF
```
seqkit stats ~/danssaltgenes/nhaA_ref.ffn
nhaA gene was found from the reference strain ATCC 15697 (locus tag BLIJ_1765). Hopefully find a faster way to do this without scrolling thru the entire genome (???)

# STEP 3: BLAST the reference against each genome to collect nhaA
```
> ~/danssaltgenes/nhaA_all.ffn

for genome in $(cat ~/danssaltgenes/tentestgenomes.txt); do
hit=$(blastn -query ~/danssaltgenes/nhaA_ref.ffn -subject /lab/binfantis/genomes/results/$genome/$genome.ffn -outfmt "6 sseqid" -max_target_seqs 1 -max_hsps 1 | head -1)
seqkit grep -p "$hit" /lab/binfantis/genomes/results/$genome/$genome.ffn >> ~/danssaltgenes/nhaA_all.ffn
done

seqkit stats ~/danssaltgenes/nhaA_all.ffn
```
First, we made an empty file that will store all the DNA sequence versions of the nhaA gene from the ten test genomes, gathered into one file so that we can align and compare them later.
Second, we set up a for loop that goes through each genome within the tentestgenomes.txt file. For the current genome that the for loop is on, we use BLAST to find nhaA by its sequence (rather than its name). More specifically, blastn takes our reference nhaA (the query, nhaA_ref.ffn) and compares it against every gene in that genome's genome.ffn file (the subject), then returns the gene that most closely resembles the reference. We ask it for just the ID of that best match (which is what -outfmt "6 sseqid" does) and we keep only the single strongest hit (which is what -max_target_seqs 1 -max_hsps 1 | head -1 does). That ID gets stored within a variable called "hit."
Third, we take that ID stored in "hit" and use seqkit grep to pull the matching gene's DNA out of the same genome's genome.ffn file, and append it (with the >>) onto the end of nhaA_all.ffn.
The last line checks the outcome

# STEP 4: Clean the headers and remove stop codons
```
seqkit seq -w 0 ~/danssaltgenes/nhaA_all.ffn | awk '/^>/{print $1; next}{print substr($0,1,length($0)-3)}' > ~/danssaltgenes/nhaA_nostop.ffn
seqkit seq -w 0 puts each sequence on a single line. The awk then does two things: for header lines (those starting with >), it keeps only the first word, which is the locus tag, and drops the description; for the DNA lines, it trims off the last three letters, which is the stop codon.
```
# STEP 5: Align
```
clustalo -i ~/danssaltgenes/nhaA_nostop.ffn -o ~/danssaltgenes/nhaA_aligned.fasta --outfmt=fasta --force -v
seqkit stats ~/danssaltgenes/nhaA_aligned.fasta
```
Clustal Omega lines up and compares the ten nhaA sequences. This saves the the result as nhaA_aligned.fasta. The seqkit stats returns the outcome.

# STEP 6: Build a tree
```
FastTree -nt -gtr ~/danssaltgenes/nhaA_aligned.fasta > ~/danssaltgenes/nhaA_tree.nwk
FastTree builds a tree showing how the ten genomes' nhaA sequences relate. -nt says the input is nucleotides (DNA), -gtr sets the DNA model, and the > saves the tree into nhaA_tree.nwk
```
# STEP 7: dN/dS calculaution
```
hyphy fel --alignment ~/danssaltgenes/nhaA_aligned.fasta --tree ~/danssaltg
```
