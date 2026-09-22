### Clustal Omega
[Clustal Omega](https://www.clustal.org/omega/) is a program for Multiple Sequence Alignment (MSA). It can take many related DNA (or protein) sequences and lines them up so that equivalent positions fall in the same column. The more related the genomes are, the easier it will be to line them up.

In our case, because we are using multiple strains of the same species, this process should theoretically be very fast.

Please see the [msa_yaml](msa_env.yaml) file to build your very own MSA enviornment within dogen

### dN/dS
dN/dS is a measurement that tells you the rate of non-synonymous mutations (did protein change?) to synonymous mutations (did protein stay the same?) within a gene. In other words, it can tell you whether the gene is under positive/diversifying selection (dN/dS >1), or conservative/stabalizing selection (dN/dS <1))

In order to calculate the dN/dS of a given gene, we must compare the same position across all of the sequences. Therefore, we must first generate an MSA, which tells us which positons (codons) correspond with one another. 
