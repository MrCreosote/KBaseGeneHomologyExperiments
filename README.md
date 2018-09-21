# KBaseGeneHomologyExperiments

[Literature search](./literature.md)

Test bed is a laptop with 32GB memory, 7500rpm disks, and a Intel i7-3840QM quad core hyperthreaded
processor at 2.8GHz with Ubuntu 14.04 running in a VirtualBox VM with 20GB memory and 4 virtual
CPUs.

## Testing with LAST

Downloaded UniRef50 (32,365,088 seqs) on 2018-09-19 12:19:16:
`nohup wget -O uniref50.gz "http://uniprot.org/uniref/?query=identity:0.5&format=fasta&compress=yes"`

Finished at 2018-09-19 22:24:48. 6.8GB gzipped, 13G unzipped.

Note UniRef100 has 154,752,138 sequences.

```
$ md5sum ../uniref50*
090d4e4c90ffcb44af17ac9a58375a76  ../uniref50
461852f4a06e63c21adbcdd748e50cd6  ../uniref50.gz
```

Took the first 40M lines of uniref50 (removing any incomplete sequence at the end) for
LAST indexing as 50M lines caused an OOM.

```
$ wc -l uniref50_40Mlines
39999997 uniref50_40Mlines
$ wc -l uniref50
215161884 uniref50
```

```
$ date; ~/bin/last/last-942/src/lastdb -p -C 2 uniref50_40Mlines.last uniref50_40Mlines; date
Thu Sep 20 12:22:14 PDT 2018
Thu Sep 20 12:45:28 PDT 2018

$ grep numofsequences uniref50_40Mlines.last.prj
numofsequences=6641431
```

Most of the search time is IO given the speed different between search 1 and all subsequent searches:

```
$ date; ~/bin/last/last-942/src/lastal uniref50_40Mlines.last UniRef50_A0A0D5A1R7.fasta; date
Thu Sep 20 20:55:57 PDT 2018
# LAST version 942

*snip*
a score=644 EG2=2.8e-69 E=1.6e-87
s UniRef50_A0A0D5A1R7 0 130 + 130 MATQAGTPSRFSNYIFKKLMETSKTQKEIAEAVGFKNQNMITMLKQGHVKLALDRVPAMAKALDLDPLDLFKIALTQFYDEQAVRMLTEIIEAGNSPAEKKILRIIRDASGENTPTLTPEREKALAELFK
s UniRef50_A0A0D5A1R7 0 130 + 130 MATQAGTPSRFSNYIFKKLMETSKTQKEIAEAVGFKNQNMITMLKQGHVKLALDRVPAMAKALDLDPLDLFKIALTQFYDEQAVRMLTEIIEAGNSPAEKKILRIIRDASGENTPTLTPEREKALAELFK

a score=200 EG2=2.7e-10 E=6.3e-19
s UniRef50_A0A2W6A2E1 6 83 + 110 PSRLQAFLYAHIEASPLTQKEIAEAVGYENPNMITILKQGDAKLALDRVVPMAEVLGVDPRHLFALALEAYYGDANARVMMKM
s UniRef50_A0A0D5A1R7 7 83 + 130 PSRFSNYIFKKLMETSKTQKEIAEAVGFKNQNMITMLKQGHVKLALDRVPAMAKALDLDPLDLFKIALTQFYDEQAVRMLTEI

Thu Sep 20 20:58:34 PDT 2018
```

2m 37s (naive extrapolation to UniRef50 = 12m 30s, 100 = 59m 48s)

```
$ date; ~/bin/last/last-942/src/lastal uniref50_40Mlines.last UniRef50_A0A0D5A1R7.fasta; date
Thu Sep 20 20:59:45 PDT 2018
# LAST version 942

*snip*
Thu Sep 20 20:59:46 PDT 2018 
```

~1s

```
In [4]: %timeit -n 10 !lastal uniref50_40Mlines.last UniRef50_A0A0D5A1R7.fasta > /dev/null
10 loops, best of 3: 522 ms per loop
```

Naive extrapolation to UniRef50 = 2.54s, 100 = 12.16s