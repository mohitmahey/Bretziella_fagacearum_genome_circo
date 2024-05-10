# Bretziella_fagacearum_genome_circo
This repo would describe the method, code used, and data acquisition for making a circo plot of the *Bretziella fagacearum* genome.
Please cite the original papers of all the tools used in making this beautiful circo plot.
This work was supported in part through computational resources and services provided by the Institute for Cyber-Enabled Research at Michigan State University.

## tools used and citations for this image

1. Circo -  A tool for visualization of large data. a great resource for learning circo at [circo.ca](https://circos.ca/documentation/tutorials/)  
**citation** - Krzywinski, M. et al. Circos: an Information Aesthetic for Comparative Genomics. Genome Res (2009) 19:1639-1645
2. Bedtops - a tool for genomic operations such as file format conversion.  
 **citation** - Shane Neph, M. Scott Kuehn, Alex P. Reynolds, Eric Haugen, Robert E. Thurman, Audra K. Johnson, Eric Rynes, Matthew T. Maurano, Jeff Vierstra, Sean Thomas, Richard Sandstrom, Richard Humbert, John A. Stamatoyannopoulos, BEDOPS: high-performance genomic feature operations, Bioinformatics, Volume 28, Issue 14, July 2012, Pages 1919–1920, https://doi.org/10.1093/bioinformatics/bts277
3. BEDtools - a tool used for genome arithmetic. \
 **citation** - Quinlan AR and Hall IM, 2010. BEDTools: a flexible suite of utilities for comparing genomic features. Bioinformatics. 26, 6, pp. 841–842.
5. SAMtools - a suite of tools for genomic data manipulation. \
 **citation** - Petr Danecek, James K Bonfield, Jennifer Liddle, John Marshall, Valeriu Ohan, Martin O Pollard, Andrew Whitwham, Thomas Keane, Shane A McCarthy, Robert M Davies, Heng Li, 
   Twelve years of SAMtools and BCFtools, GigaScience, Volume 10, Issue 2, February 2021, giab008, https://doi.org/10.1093/gigascience/giab008

## Picture of the circo plot of *Bretziella fagacearum* genome
![Circo plot](https://github.com/mohitmahey/Bretziella_fagacearum_genome_circo/blob/main/circos.png)
the Circo plot shows from outermost to innermost ring a) the length (MBp) of the  chromosomes labeled one through nine. b) GC-content distribution with high GC(blue) and low GC(yellow) regions. c)gene density per 10kb of genomic region(0 - 0.006). d)Location of genes across the whole genome.

## getting data files
For the track data, genome file, and gff was used. For the Circo data, all the input files are in tab-delimited format.

### 1. karyotype -  size of the chromosomes

To get the size of the chromosomes, SAMtools was used.
This command gives general information about the genome including size.
```
samtools faidx Brefa1_AssemblyScaffolds.fasta
```
To only get the length of the chromosomes 

```
cut -f1,2 Brefa1_AssemblyScaffolds.fasta.fai > scaffold_sizes.txt
```

### 2. GC-content across genome

To get GC content per 10000bp of the genome, we can use the bedtools "nuc" function
First, we will need a .bed file with the partition of the genome into 10000bp chunks. this will be used as a reference bed for "nuc" function

to get genomic_regions.bed file we can use makewindows function of bedtools
```
bedtools makewindows -g scaffold_sizes.txt -w 10000 > genomic_regions.bed
```

"nuc" provides GC content and other stats as different columns, which was removed in Excel later
```
bedtools nuc -fi Brefa1_AssemblyScaffolds.fasta -bed genomic_regions.bed > gc_content
```

### 3. Gene-density plot 
Gene density is calculated as the number of predicted genes per 10000bp

first, all gff file was parsed for genes using awk to get a gff file with only gene positions
```
awk '$3 == "gene"' Brefa1_GeneCatalog_20200715.gff3 > output_genes.gff3
```

to count the number of genes per 10000 bp, we will utilize the "intersect" function
```
bedtools intersect -a genomic_regions.bed -b output_genes.gff3 -c > gene_counts_per_region.bed
```
to calculate density we are will utilize awk 

```
awk '{print $0, $NF/1000}' gene_counts_per_region.bed > gene_density_per_region.bed
```
gene_density_per_region.bed file was renamed and saved as gene_density.txt 

### 4. gene locations 
The tile graph shows the position of the genes across the genome
The gene position was calculated from gff3 files

the gff file was parsed with awk to only get info of genes
```
awk '$3 == "gene"' Brefa1_GeneCatalog_20200715.gff3 > output_genes.gff3
```
gff2bed function of the bedtops toolkit was used to convert the output_genes.gff3 to output_genes.bed
Using Excel only the first 3 columns were kept and saved to be used to plot the track as gene_position.txt











