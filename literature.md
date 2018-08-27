# Literature search

Notes regarding a literature search for gene homology methods. 

## Intent

Deploy a joint JGI/KBase Gene Homology service for performing basic BLAST-like searches.
< 30% identity searches (e.g. HMMR / PFAM territory) are not covered.

## Literature

Note this is not a comprehensive search - that would take months as there are hundreds, maybe even
thousands, of search methods.

There seems to be two major categories of methods: alignment based and alignment-free. The
former is much more established and has many software packages available. The latter appears to
be much less mature and it so far has been difficult to find easy to use, established packages.

### Alignment based homology

Standard Needleman/Wunch / Smith/Waterman family searches.

[MMSeqs2](https://www.nature.com/articles/nbt.3988) authors,
as of November 2017, claim their package is the fastest and most sensitive, beating
[LAST](https://genome.cshlp.org/content/early/2011/01/05/gr.113985.110.abstract),
[RAPSearch2](https://academic.oup.com/bioinformatics/article/28/1/125/218953),
and [DIAMOND](https://www.nature.com/articles/nmeth.3176), among others. MMSeqs2
requires NL * 7B of memory where N is number of sequences and L is not defined but I presume is
the average sequence length, but supports MPI and can split databases into arbitrarily sized
chunks.

However, a group with no common authors with the DIAMOND paper released
[AC-DIAMOND](https://academic.oup.com/bioinformatics/advance-article/doi/10.1093/bioinformatics/bty391/4996593)
in May 2018, which claims to be faster and approximately as sensitive as MMSeqs2. They don't
appear to describe the settings used to run MMSeqs2. AC-DIAMOND used up to 57Gb of memory
to align 12Gbases of amino acid sequence to NCBI-nr.

### Alignment free homology

There are a zillion different sequence distance algorithms and it's not clear which, if any,
have mainstream traction. Many of the algorithms appear to be of demonstration-only quality,
without an accompanying software package for actual every day use.

Several reviews compare various sets of algorithms.

[Zielezinski et. al.](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-017-1319-7)
(marked as Z from now on)
has an [accompanying website](http://combio.pl/alfree) that evaluates 34 different word (e.g. kmer)
and information theory based methods. They are compared only against Smith-Waterman, and no other
alignement based algorithms. Google distance (TODO: cite) and Bray-Curtis (TODO: cite) distance
were the top performers regarding accuracy.

The measures were, in order of accuracy:
* d google
* d Bray-Curtis
* d Eseq1
* d Eseq2
* d Canberra
* Smith Waterman
* d LZ*
* d S
* d E
* d Minkowski
* d abs_mean
* d Manhattan
* d RTD
* d abs_mult1
* d abs_mult2
* d Chebyshev
* d LZ**1
* d LCC
* d W
* d abs_mult
* d LZ*1
* d EVOL1
* d EVOL2
* d FFP
* d Sorensen-Dice
* d Jaccard
* d 2
* d KL
* d Hamming
* d LZ
* d BBC
* d NCD
* d CV
* d LZ1






