*Background on sequencing and kneaddata?*

Each DNA fragment is sequenced from both ends via Illumina, which can produce two reads: R1 (forward) and R2 (reverse). The two reads come as a matched pair in separate files, and they line up position-for-position. Notably, if a DNA fragment is too large, then the middle of the fragment may not be read at all.

KneadData is a program that trims the low-quality ends off reads. After trimming, it sorts each read based on what survived. If both R1 and R2 are still good, the pair stays together, outputting both R1 and R2. If too much gets trimmed and one read becomes too short, it is deleted, which can break the pair. When only one mate survives, that lonely read is moved to an unmatched file. We can disregard the  unmatched files.

The two paired files should contain the same number of reads, therefore they should be of similar size. In order to test if the files are proper:

gzip -t ~/Desktop/kneaddata/A00062_combined.fastq.gz

gzip is a command that compresses files, and the -t tag is “testing” (does not actually do anything). This command tests if a file is proper, and a file passes if there is no output. In the case of A00062, this command fails.

*Goals for next week*
- Test the following file in baqlava: lab/sequencing/processed/kneaddata within dogen, using AA00447 file instead (already within my Desktop)
- Push all Obsidian notes
- Install, make a protocol for, and familiarize myself with WAAFLE. Install within a different conda environment.

