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

If you take [MMSeqs2](https://www.nature.com/articles/nbt.3988)'s authors at face value,
as of 2017 their package was the fastest and most sensitive, beating
[LAST](https://genome.cshlp.org/content/early/2011/01/05/gr.113985.110.abstract),
[RAPSearch2](https://academic.oup.com/bioinformatics/article/28/1/125/218953),
and Diamond, among others.

TODO check MMSeqs2 references:  
[AC-Diamond](https://academic.oup.com/bioinformatics/advance-article-abstract/doi/10.1093/bioinformatics/bty391/4996593?redirectedFrom=PDF)  
[General review of sequence search](https://currentprotocols.onlinelibrary.wiley.com/doi/abs/10.1002/cpps.71)  