# KBaseGeneHomologyExperiments

[Literature search](./literature.md)

Test bed is a laptop with 32GB memory, 7500rpm disks, and a Intel i7-3840QM quad core hyperthreaded
processor at 2.8GHz with Ubuntu 14.04 running in a VirtualBox VM with 20GB memory and 4 virtual
CPUs.

## Testing with LAST

Downloaded UniRef50 (32,365,088 seqs) on 2018-09-19 12:19:16:
`nohup wget -O uniref50.gz "http://uniprot.org/uniref/?query=identity:0.5&format=fasta&compress=yes"`

Finished at 2018-09-19 22:24:48. 6.8GB gzipped, 13G unzipped.

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
```
