Discovery of Nuclear Mitochondrial Insertions (dinumt)
======================================================

Description
-----------

This software is designed to identify and genotype nuclear insertions of mitochondrial origin from whole genome sequence data. It consists of two programs: dinumt (di-nu-mite), which identifies sites of insertions in a single sample and gnomit (geno-mite), which genotypes those sites across multiple samples. There is an additional program named clusterNumtsVcf which will merge sites identified in multiple samples into a single merged file for genotyping.

Required third-party resources 
------------------------------

A number of third party software packages are required by these programs:

* samtools:  http://samtools.sourceforge.net/
* exonerate:  http://www.ebi.ac.uk/~guy/exonerate/
* vcftools:  http://vcftools.sourceforge.net/

In addition, you will need:

* reference genome in fasta format (e.g. hs37d5.fasta)
* individual MT sequence (e.g. MT.fa or chrM.fa)

The genotyping step requires the use of a sample index file containing various sample-level information (mean insert size, coverage, etc). A template has been provided, and the relevant data can be obtained by using GATK (DepthOfCoverage walker) and Picard (CollectInsertSizeMetrics) or custom scripts.

Parameters
----------

Additional information about various parameters below:

* `--len_cluster_include` : width of window to consider anchor reads as part of the same cluster, typically calculated as `mean_insert_size` + 3 * `standard_deviation`
* `--len_cluster_link`    : width of window to link two clusters of anchor reads in proper orientation, typically calculated as 2 * `len_cluster_include`
* `--max_read_cov`        : maximum read depth at potential breakpoint location, used to filter out noisy regions of the genome, typically calculated as 5 * `mean_coverage`
* `--output_support`      : output all sequence reads supporting an insertion event in SAM format to filename in `--support_filename option`
* `--mask_filename`       : bed file of all numts annotated in reference sequence, one is provided for GRCh37 but additional versions can be obtained from UCSC Genome Browser
* `--min_map_qual`        : mininum mapping quality required for anchor read to be considered for cluster support, default is 10 but can be adjusted as needed

Example workflow
----------------
An example workflow would be as follows:

(can be done in parallel)
`dinumt-0.0.22.pl --mask_filename=refNumnts.bed --input_filename=sample1.bam --min_reads_cluster=1 --include_mask --output_filename=sample1.vcf --prefix=sample1 --len_cluster_include=577 --len_cluster_link=1154 --insert_size=334.844984 --max_read_cov=29 --output_support --support_filename=sample1_support.sam
`dinumt-0.0.22.pl --mask_filename=refNumnts.bed --input_filename=sample2.bam --min_reads_cluster=1 --include_mask --output_filename=sample2.vcf --prefix=sample2 --len_cluster_include=577 --len_cluster_link=1154 --insert_size=334.844984 --max_read_cov=29 --output_support --support_filename=sample2_support.sam
.
.
`dinumt-0.0.22.pl --mask_filename=refNumnts.bed --input_filename=sampleN.bam --min_reads_cluster=1 --include_mask --output_filename=sampleN.vcf --prefix=sampleN --len_cluster_include=577 --len_cluster_link=1154 --insert_size=334.844984 --max_read_cov=29 --output_support --support_filename=sampleN_support.sam --reference=hs37d5.fa
(end parallel)

`grep ^# sample1.vcf > header.txt
`cat *vcf | grep -v ^# | vcf-sort.pl | clusterNumtsVcf.pl > data.txt
`cat header.txt data.txt > merged.vcf

(merged vcf can be split into smaller pieces with multiple sets of sites run in parallel, if need be)
`gnomit-0.0.16.pl --input_filename=merged.vcf --mask_filename=refNumts.bed --info_filename=sampleInfo --output_filename=merged_geno.vcf --samtools=samtools --reference=hs37d5.fa --breakpoint --min_map_qual=13 --dir_tmp=/tmp --exonerate=exonerate --mt_filename=MT.fa

Contact
-------
Questions: Please contact Ryan Mills at remills@umich.edu
04/16/2014
