# AlignQC

See wiki for the most up-to-date manual. 

[https://github.com/jason-weirather/AlignQC/wiki](https://github.com/jason-weirather/AlignQC/wiki)

##### Instant Start (via docker and example data):

```
$ git clone https://github.com/jason-weirather/AlignQC.git
$ cd AlignQC
$ docker run -v $(pwd)/example_data/:$(pwd)/example_data/ vacation/alignqc alignqc analyze -g $(pwd)/example_data/chr21chr22chrM.fa.gz -t $(pwd)/example_data/chr21chr22chrM.gencode25.gtf.gz $(pwd)/example_data/chr21chr22chrM.bam -o $(pwd)/example_data/myoutput.xhtml
$ cd example_data
```

##### Easy install via conda

```
$ conda create -n alignqc -c vacation alignqc
$ source activate alignqc
```

#### Install via pip (requires R installed and python 2.7)

```
$ pip install AlignQC
```

See basic stats, error patterns, and more in the example data from the docker run example:

![Basic stats](https://i.imgur.com/oHgEM6t.png)

![Error patterns](https://i.imgur.com/pIG0W0F.png)

##### What you can do

**Generate a report** on sequencing alignments to understand read alignments vs read sizes, error patterns in reads, annotations and rarefractions.

**Share your reports** with anyone who has an internet browser.

Ultimately this software should be suitable for assessing alignments of from a variety of sequencing platforms and a variety of sequence types.  The focus for the first version of this software is on the transcriptome analysis of third generation sequencing data outputs.

##### Report Generation Requirements
- R
- python 2.7+

##### Report Viewing Requirements
- A Modern Web Browser

##### Installation

You can install to your machine via pip or conda.

If you install via conda, or use the docker requirements are taken care of for you. Otherwise you will need python 2.7 and `Rscript` should be installed in your path, if it is not, you can specify a location during the `analysis` command with the `--rscript_path` option.

You can install via pip with

`$ pip install AlignQC`

You can install via conda, but I highly recommend creating an environment for AlignQC

`$ conda create -n alignqc -c vacation AlignQC`
`$ source activate alignqc`

##### Fast start
The following command should be sufficient for assessing a long read alignment.

`alignqc analysis long_reads.bam -g ref_genome.fa -t ref_transcriptome.gtf -o long_reads.alignqc.xhtml`

**Note:** We recommend using the `-t` or `--gtf` option for the reference transcriptome. A GenePred transcriptome annotation file may be alternatively used with the `--gpd` option, but there is an issue with these right now where genePred files with periods `.` in the CDS start and start do not parse, so I recommend using the `GTF` format input. If you must use GPD and you have periods in the CDS set these to your transcript start and stop (integer values).

If you don't readily have your reference genome or reference annotation available you can try the following.

`alignqc analysis long_reads.bam --no_genome --no_transcriptome -o long_reads.alignqc.xhtml`

## AlignQC programs
Currently AlignQC only offers the `analysis` program.

## Analysis
`alignqc analysis`

The analysis command is the most basic command for assessing an alignment.  It provides reports and plots in xhtml format.

### Inputs
A complete list of optional commands for each sub command is available with the `-h` option.

`alignqc analysis -h` 

Will report all analysis required and optional inputs.

#### 1. BAM format alignment file
The preferred format for transcriptome analysis is GMAP output (the 'samse') format.  Default output, or an output that can produce multiple alignment paths for a read is recommended if you want the ability to assess chimeric reads.

You can convert the SAM output of GMAP into BAM format using Samtools.
http://www.htslib.org/

Any properly formated BAM file should work however this software has only been tested with GMAP and hisat outputs at the moment.  
http://samtools.github.io/hts-specs/SAMv1.pdf

Please note that analyzing very large hiseq datasets has not been tested and memory requirements have not been optimized for this type of run.  If you want to check error patterns of HISEQ downsampling the data is advised.

#### (optional) 2. Genome fasta file
The reference genome `-g` these sequences were aligned to, in fasta format, can allows you to assess the error rates and error patterns in the alignments.

If you choose not to use a reference genome you must explicitly specify `--no_genome`

#### (optional) 3. GTF format annotation file
Providing an annotation file `-t` or `--gtf` provides context such as known transcripts, and exons, introns, and intergenic regions to help describe the data.  It is also necessary for rarefraction curves.

If you choose not to use a reference annotation you must explicitly specify `--no_transcriptome`

Genepred files can alternatively be used `--gpd` to describe the reference transcriptome, but there is currently an issue where `.` periods in the CDS sequence of some files causes an error, so sticking with GTF is recommended at the moment unless you know your GPD format is free of these.

The genePred format is described here.
http://www.healthcare.uiowa.edu/labs/au/IDP/IDP_gpd_format.asp

And it is also described here as "Gene Predictions and RefSeq Genes with Gene Names" genePred format described by UCSC.
https://genome.ucsc.edu/FAQ/FAQformat.html#format9

- geneName
- name
- chrom
- strand
- txStart
- txEnd
- cdsStart
- cdsEnd
- exoncount
- exonStarts
- exonEnds

### Outputs
At least one output format must be specified.

To view the output xhtml Mozilla Firefox or Google Chrome browser is recommend.

Since the recommended output type contains large URI data embedded in the xhtml page, Internet Explorer will likely not be compatible.  The memory requirements of the regular output may strain some systems.

If you only want to share the visual results with others we recommend the `--portable_output` option because this version contains only the main text and png files.

If accessing the embedded data in the xhtml is a problem, you can output the data in a folder format `--output_folder`, which can provide you more convenient access.


#### (option 1) Standard xhtml output
`-o` or `--output`

The recommended output of this software is a single xhtml file that contains all the relevant files from the analysis embeded as base64 encoded URI data.  

This means you can click any of the links in the document and the browser will download the data of interest (i.e. PDF format versions of a figure) from within the document.

Bed tracks compatible with the UCSC genome browser are also provided.

#### (option 2) Portable xhtml output
`--portable_output`

This output is recommended if you want to email these results or share them over a bandwidth limited connection.  This format only has the png images and webpage text.  Links are disabled.

#### (option 3) Output folder
`--output_folder`

Store an output folder that contains all the data and figures.  An xhtml file is still available in this folder.




