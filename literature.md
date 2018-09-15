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

As I continue to read reviews and papers, it's become abundantly clear there are far too many
methods and packages available to evaluate them all, or even a substantial subset, and there
doesn't appear to be clear agreement in the field regarding which methods are the front runners. A
comprehensive evaluation of the best ALF methods vs. the best AL methods would be very helpful
but doesn't seem to exist. There will need to be a winnowing process to determine which packages
to spend cycles on, if any. My initial proposal is that

1. There must be a ready to use software package for the method (e.g. ALFree, CAFE) or
   the method must be quick to understand and implement (unclear on how to judge this for now).
2. There must be some indication that it's near the top of the field for accuracy and performance,
   whether in a review or via an analysis in the method paper.
3. The method needs to be noted as specifically targeting short sequences.
4. The method must be presented in a paper, not a thesis or dissertation.
5. The method must be present in a review or paper less than 10 years old.

I'm also not reading reviews more than 10 years old given the rapidity of change in the field.

Several reviews compare various sets of algorithms.

#### [Zielezinski et. al., 2017](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-017-1319-7)
(notated as Z from now on)  
Has an [accompanying website](http://combio.pl/alfree) that evaluates 33 different word (e.g. kmer)
and information theory based methods on a dataset derived from SCOP. They are compared only
against Smith-Waterman, and no other alignment based algorithms.
[Google distance (2008)](https://ieeexplore.ieee.org/abstract/document/4631601/)
and [Bray-Curtis distance](https://en.wikipedia.org/wiki/Bray%E2%80%93Curtis_dissimilarity)
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

Note that I believe that the author in this case is using `d` to mean distance - so `d google`
is the google distance metric - but in papers below `d` also refers to the `D 2` statistic family,
including `D 2`, `D S2`, `D *2`, `d S2`, and `d *2`. This makes it somewhat confusing to
interpret the method lists.

Other methods noted in the review but not evaluated:
* [MissMax (2016)](https://almob.biomedcentral.com/articles/10.1186/s13015-016-0072-x)
* [wre.kr., S2.kr. (2008)](https://academic.oup.com/bioinformatics/article-abstract/24/20/2296/259406)  
  Did not read due to the senescent status of this algorithm.
* [decaf+py (2007)](https://academic.oup.com/sysbio/article/56/2/206/1686184)  
  Did not read due to the senescent status of this algorithm.

#### [Lu et. al., 2017](https://academic.oup.com/nar/article/45/W1/W554/3791208)
Presents CAFE, a software suite for performing kmer-count based alignment free distance
measurements. Tests against vertebrate genomes and gut metagenomes.

```
The comparison involves three dissimilarity measures
based on background adjusted k-mer counts including
CVTree, d *2 and d S2, 10 conventional measures based on
k-mer counts, including Canberra, Ch, Cosine, Co-phylog,
d2, Eu, FFP, JS, Ma and Pearson, and 15 measures based
on presence/absence of k-mers including Anderberg, Antidice,
Dice, Gower, Hamman, Hamming, Jaccard, Kulczynski,
Matching, Ochiai, Phi, Russel, Sneath, Tanimoto and
Yule.
```

`d *2`, `d S2`, CVTree, Canberra, and co-phylog performed the best in the genome tests for
accuracy. Efficiency results are not reported. Peak memory for `d *2` is repored as 25-50G. No
comparisons against alignment-based methods were reported.


#### [Bernard, et. al., 2016](https://www.nature.com/articles/srep28970)
(notated as B from now on)  
Evaluates 9 methods using simulated data.
[co-phylog (2013)](https://academic.oup.com/nar/article/41/7/e75/1067663),
`d S2` [a (2009)](https://www.liebertpub.com/doi/abs/10.1089/cmb.2009.0198)
[b (2010)](https://www.liebertpub.com/doi/abs/10.1089/cmb.2010.0056),
cvt, and kmacs performed well, although cvt and kmacs did not perform well when tested under
conditions of genome rearrangement. co-phylog and d S2 were also much faster than cvt and
kmacs. The methods were not compared to any alignment based algorithm.

The methods were:
* co-phylog
* `d S2`
* cvt
* ffp
* spaced
* acs
* gram
* kmacs
* kr

#### [Song et. al., 2013](https://academic.oup.com/bib/article/15/3/343/182355)
(notated as S from now on)
Evaluates 6 methods: `n2`, `d *2`, and `d S2` performed similarly and best, with `S2`
performing well under specific circumstances. The methods were not compared to any alignment
based algorithm.

The methods were:
* Hao
* `n2`
* `d *2`
* `d S2` (also evaluated in B)
* Jensen-Shannon
* `S2`

#### [Luczak et. al., 2017](https://academic.oup.com/bib/advance-article/doi/10.1093/bib/bbx161/4696316)
This review is pretty difficult to interpret in the context of determining the best AF method(s).
They evaluate the performance and runtime of 33 different difference statistics, the square of
the statistics, and multiplicative combinations of the statistics against some fairly small
data sets (most with short sequences).

```
The following is a list of the 33 statistics evaluated in this article: Hellinger,
Manhattan, Euclidean, v2, normalized vectors, harmonic mean, Jeffrey divergence,
K-Divergence, Pearson correlation coefficient, squared chord, Kullback–Liebler
conditional divergence, Markov similarity, intersection, rre_k_r, D2z, SimMM,
EuclideanZ, EMD, Spearman, Jaccard, LD, DS2 , AFd, mismatch, Canberra,
Kulczynski Similarity 1, Kulczynski Similarity 2, similarity ratio,
Jensen–Shannon Divergence, D*2, N2r, N2rc and N2rrc.
```

They correlate the statistics against the  %ID generated by Needleman-Wunch and find very
different results at different %ID thresholds, but in all cases the best performers
(r2 = 0.97-0.98) were multiplicative combinations of statistics where one of the statistics
took the overall length of the sequence into account. For most cases, the top performers
possessed a simple length difference as one of the two multiplicatively combined statistics
and the other varied widely, implying that much of the comparative power of the combined
statistic was based on the lengths of the sequences.

They also look for nearest neighbors via local alignment but the performance didn't seem stellar
based on the brief discussion.

#### Other methods
* [SSAW (2018)](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-018-2155-9)
* [FASTCAR (2018)](https://www.biorxiv.org/content/early/2018/07/31/380824)
* [LZW-Kernel (2018)](https://academic.oup.com/bioinformatics/advance-article/doi/10.1093/bioinformatics/bty349/4993879)
* [Similarity Projection, (2017)](https://ieeexplore.ieee.org/abstract/document/8109151/)  
  Dataset 1: 2x faster than BLAST, somewhat better performance. Dataset 2: slightly faster than
  BLAST, somewhat worse performance.
* [K2, K*2 (2017)](https://academic.oup.com/bioinformatics/article/34/10/1682/4747886)  
  Compares itself favorably against `D2`, `D *2`, `D s2 (D sh2)`, `D z2`, `D Mk`, `CFP`,
  `DV`, `Shi`and `WVF` for both accuracy and speed.
* [ACSk (2015)](https://link.springer.com/chapter/10.1007/978-3-319-16706-0_1)
* [kWIP (2017)](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005727)
* [Weighted D2 (2017)](https://www.sciencedirect.com/science/article/pii/S0378437117303540)
* [rasbhari (2016)](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005107)
* [Entropic Profiles with Enhanced Suffix Arrays and Truncated Suffix Trees (2016)](https://ieeexplore.ieee.org/abstract/document/7605504/)  
  Compares 3 related EP methods against an older EP method.
* [ALFRED (2016)](https://www.liebertpub.com/doi/abs/10.1089/cmb.2015.0217)  
  Average common substring with hamming distance < k approach. O(n logk n) complexity. Doesn't
  compare itself to any other methods. 
  * [Follow up paper](https://link.springer.com/chapter/10.1007/978-3-319-89929-9_14) -
    Purely theoretical.
* [Cis sequence complex DNA networks (2016)](http://journals.sagepub.com/doi/abs/10.4137/EBO.S40474)  
  Creates graphs where small (2-5) kmers are nodes and adjacency determines edges. Determines
  sequence difference via feature extraction from the graphs. Not compared against any other
  methods.
* [kmacs with length distribution (2017)](https://arxiv.org/abs/1709.01371)  
  kmacs method with alterations. Compares itself with `andi` and `fswm` with generally similar
  results. Efficiency is not compared.
* [Under2 (2017)](https://link.springer.com/chapter/10.1007%2F978-3-319-59826-0_14)  
  A review and presentation of the `Under2` statistic, which is favorably compared against
  the `d 2`, `d S2`, and `d *2` statistics, but no code is provided. It also notes that in
  general, none of the methods perform well on short sequences.


#### Other reviews
* [Ren et. al., 2018](https://www.annualreviews.org/doi/abs/10.1146/annurev-biodatasci-080917-013431)
  is from the same group as S. It doesn't provide a clear indicator of superior alignment-free
  methods.

* [Bonham-Carter et. al., 2013](https://academic.oup.com/bib/article/15/6/890/177681) reviews,
  but does not otherwise evaluate, a number of ALF methods.

#### Other articles
* [Catteaneo et. al., 2015](https://ieeexplore.ieee.org/abstract/document/7349910/)
  implements several dis/similarity methods (Euclidean, squared Euclidean, `D 2`, `D S2`, `D *2`,
  FFP, spaced, and their own implementation of co-phylog), and compares run time with and without
  Hadoop based MapReduce parallelization on large (>50Mbases) sequences. The runtimes are fairly
  similar other than co-phylog which is ~2x slower on larger sequence counts. No accuracy
  measurements are made.