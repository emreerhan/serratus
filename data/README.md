# Serratus Data / Resources directory

**Serratus Bucket($S3)**: `s3://serratus-public/`

For `aws-cli` access use `aws s3 cp s3://serratus-public/<file_path>`.
You can also list the directory with `aws s3 ls s3://serratus-public/`
For `wget`/`curl` access use `wget https://serratus-public.s3-us-west-2.amazonaws.com/<file_path>`

## `./out` : Serratus Output data
Currently output from ~50 librarires using standard alignment
- `bam` : Aligned output file + index, SRA accession named
- `flagstat` : Flagstat files for bam files

## `./seq` : Genomes / Pan-Genomes
Reference gene/genome sets and their associated index files.

- `cov0`  : All CoV sequences from NCBI
	- NCBI search: `"(Coronaviridae) AND "viruses"[porgn:txid10239]"`
	- Date Accessed: 2020/03/30
	- Results: 33296

- `hgr1`  : Human rDNA testing sequence
	- From [this publication](https://www.biorxiv.org/content/10.1101/118760v2)


## `./sra` : SraRunInfo Tables (.csv.gz)
SRA Accession and Run Information master tables. Accessed via SRA website and the following basic filter set

```
"type_rnaseq"[Filter] AND cluster_public[prop] AND "platform illumina"[Properties] AND "cloud s3"[Properties] NOT "scRNA"[All Fields] AND <SUBFILTER>
```

- **Non-Human, Non-Mouse Mammals**
	- `BASE AND "Mammalia"[Organism] NOT "Homo sapiens"[Organism]) NOT "Mus musculus"[orgn]`
	- Date Accessed: 2020/03/28
	- Results: 66926, 0.15 PB

- **Human**
	- `BASE AND "Homo sapiens"[Organism]`
	- Date Accessed: 2020/03/05
	- Results: 520257, 4.75 PB

- **Mouse**
	- `BASE AND "Mus musculus"[orgn]`
	- Results: 539233
	- Not accessed

- **Vertebrates, Non-mammal**
	- `BASE NOT "Mammalia"[Organism] NOT "Homo sapiens"[Organism] NOT "Mus musculus"[orgn]`
	- Date Accessed: 2020/03/29
	- Results: 74532, 0.115 PB

- **Invertebrates**
	- `BASE NOT "Vertebrata"[Organism]`
	- Date Accessed: 2020/03/30
	- Results: 403639, 0.7 PB

- **HCT116 RNAseq**
	- For testing; ca. 1000 entries of human HCT116 cell line


## `./test-data` : Data for testing

**Sequence Files**
- `../bam/` : aligned bam files for breaking into blocks
- `../bam-block` : bam file output of fq-blocks requiring merging
- `../fq/`  : sequencing reads of various length
- `../fq-block` : fq files broken into 'blocks'
- `../out` : Example output data of re-aligned reads

## `./var/` : Assorted nuts and bolts
;)

