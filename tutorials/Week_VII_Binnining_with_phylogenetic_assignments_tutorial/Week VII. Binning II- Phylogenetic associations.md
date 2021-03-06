# Week VII. Binning II- Phylogenetic associations
*SIOB278 Ocean 'Omics: Metagenome Data Processing & Analysis workshop tutorial*

* Last edit: 5/12/17
* Jessica Blanton

**Tutorial Goals**:

1. Assignments of phylogenetic associations to contigs
1. Phylogenetic profiling of assembly

**Software used**:

* [**MEGAN6, community edition**](https://ab.inf.uni-tuebingen.de/software/MEGAN6)
* ([BLAST](https://www.ncbi.nlm.nih.gov/books/NBK279670/) or [DIAMOND](https://github.com/bbuchfink/diamond))


## Overview
###On Gene-based Phylogenetic association of assembly contigs

This approach to taxonomic classification hinges on the idea that sequence divergence is related to evolutionary distance, aka phylogenetic relationships

This type of analysis starts by obtaining the results from homology-based searches of the contigs. The next step is to assess the taxonomic identity of the resulting hits. Finally, there must be some means to decide consensus between taxonomy of the hits from the same sequence fragment, which is then assigned to the contig. A number of programs perform this last step, namely the **LCA algorithm from MEGAN** and the **DarkHorse algorithm**. 

- Huson DH, Auch AF, Qi J, Schuster SC. MEGAN
analysis of metagenomic data. Genome Res 2007;17(3):377–
86 [link](http://genome.cshlp.org/content/17/3/377)

- Podell S, Gaasterland T. DarkHorse: a method for genomewide
prediction of horizontal gene transfer. Genome Biol S
2007;8(2):R16 [link](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2007-8-2-r16)


### On running a homology search of an assembly vs known sequences
This is commonly done using BLAST or the newer stand-in DIAMOND, which takes your query sequences and runs them against a large informative database- like the whole of NCBI's Non-Redundant Database.

For many uses, **DIAMOND ~ BLAST**, [Check out this writeup here](https://github.com/Open-Data-Science-at-SIO/BUG-Resources/wiki/Using-Diamond-for-BLAST-like-searches)

While the process is identical to the one used to search for 16S/18S rRNA gene matches with the SILVA database, the nr database is much larger- ~30GB compressed. So while we will provide you with the command for the search, this tutorial does not have you actually run it, but instead the output that was run on the Triton Super Computer. 

## Getting your computer set up 
Tutorial requires only your laptop!

1. **Install MEGAN6 Community Edition onto your laptop** [from this website](http://www-ab.informatik.uni-tuebingen.de/data/software/megan6/download/welcome.html)

1. **Download and unzip the file which maps NCBI protein accession numbers to taxonomy ID** using this link [prot_acc2tax-Nov2016.abin](http://ab.inf.uni-tuebingen.de/data/software/megan6/download/prot_acc2tax-Nov2016.abin.zip)
Takes ~30 minutes
		
	- move unzipped file to application directory

1. **Download the DIAMOND search results file** [G7mega5.daa](https://www.dropbox.com/s/mkbwfi2wkrp6dc4/G7mega5.daa?dl=1) 

	- This file was generated by running the following commands with the assembly file `G7_subst5_megahit_041617/final.contigs.fa`, and the nr formatted for a DIAMOND search `nr.dmnd`.
		
		> diamond blastx --query GUM007_subst5_megahit_041617.fa --db nr.dmnd --daa G7mega5.daa 

	We used DIAMOND instead of BLAST since it is much faster than BLAST for large databases like nr. Still, using 7 cores, it took 1h 47m to search the 15M query against a 55G database. 
		
## Use MEGAN to make phylogenetic assignments 


**2. Open the MEGAN program**

**2. Import the diamond results file to MEGAN**

- File dropdown list--> MEGANize DAA File --> choose your G7mega5.daa --> 
- Select "Taxonomy" tab --> check box to Analyze Taxonomy content --> Make sure that file `prot_acc2tax-Nov2016.abin` is selected as the taxonomy acession mapping file --> 
- Apply

**3. Export taxonomic assignments at phylum level from MEGAN**:
	
- Tree dropdown list --> select Rank... --> collapse to phylum rank
	
- Collapse Eukaryotic node (select node, and right-click) **[screenshot open new window](https://www.dropbox.com/s/qt8f013cskzdtoa/4.png?dl=0))
	
- Select desired leaves (tips only) **[screenshot open new window](https://www.dropbox.com/s/0z5ed6s7co0w1tu/5.png?dl=0)

- File dropdown list--> extract reads... --> create new directory called `exported_tax_fastas/` --> check box "Include Summarized Reads" --> Extract  **[screenshot open new window](https://www.dropbox.com/s/vl5iqusglrg0l5p/6.png?dl=0) 

**4. Get list of all groups exported with some file slicing**:

In a terminal emulator:
	
	cd ~/Dropbox/R_directory/Metagenomic_analyses/GC_v_Coverage/exported_tax_fastas
	ls 
			Actinobacteria__phylum_reads.fasta
			Bacteroidetes_reads.fasta
			Candidatus_Tectomicrobia_reads.fasta
			Chlamydiae_reads.fasta
			Chlorobi_reads.fasta
			Cyanobacteria_reads.fasta
			Elusimicrobia_reads.fasta
			Eukaryota_reads.fasta
			Euryarchaeota_reads.fasta
			Firmicutes_reads.fasta
			Not_assigned_reads.fasta
			Planctomycetes_reads.fasta
			Proteobacteria_reads.fasta
			Spirochaetes_reads.fasta
			Thaumarchaeota_reads.fasta
			
From the list above, annotate contigs with taxonomic assignment from file lable, and concatenate.  The following should be edited to match your results from MEGAN (you may have fewer or more phyla assigned depending on your export selections).

	grep ">" Actinobacteria__phylum_reads.fasta | sed 's/>//g' | sed 's/$/;Actinobacteria__phylum/g' > ctg_tax.txt
	grep ">" Bacteroidetes_reads.fasta | sed 's/>//g' | sed 's/$/;Bacteroidetes/g' >> ctg_tax.txt
	grep ">" Candidatus_Tectomicrobia_reads.fasta | sed 's/>//g' | sed 's/$/;Tectomicrobia/g' >> ctg_tax.txt
	grep ">" Chlamydiae_reads.fasta | sed 's/>//g' | sed 's/$/;Chlamydiae/g' >> ctg_tax.txt
	grep ">" Chlorobi_reads.fasta | sed 's/>//g' | sed 's/$/;Chlorobi/g' >> ctg_tax.txt
	grep ">" Cyanobacteria_reads.fasta | sed 's/>//g' | sed 's/$/;Cyanobacteria/g' >> ctg_tax.txt
	grep ">" Elusimicrobia_reads.fasta | sed 's/>//g' | sed 's/$/;Elusimicrobia/g' >> ctg_tax.txt
	grep ">" Eukaryota_reads.fasta | sed 's/>//g' | sed 's/$/;Eukaryota/g' >> ctg_tax.txt
	grep ">" Euryarchaeota_reads.fasta | sed 's/>//g' | sed 's/$/;Euryarchaeota/g' >> ctg_tax.txt
	grep ">" Firmicutes_reads.fasta | sed 's/>//g' | sed 's/$/;Firmicutes/g' >> ctg_tax.txt
	grep ">" Not_assigned_reads.fasta | sed 's/>//g' | sed 's/$/;Not_assigned/g' >> ctg_tax.txt
	grep ">" Planctomycetes_reads.fasta | sed 's/>//g' | sed 's/$/;Planctomycetes/g' >> ctg_tax.txt
	grep ">" Proteobacteria_reads.fasta | sed 's/>//g' | sed 's/$/;Proteobacteria/g' >> ctg_tax.txt
	grep ">" Spirochaetes_reads.fasta | sed 's/>//g' | sed 's/$/;Spirochaetes/g' >> ctg_tax.txt
	grep ">" Thaumarchaeota_reads.fasta | sed 's/>//g' | sed 's/$/;Thaumarchaeota/g' >> ctg_tax.txt
	
	wc -l ctg_tax.txt 
	    3366 ctg_tax.txt

Use the "cut" command to make sure the delimter between contig and assignment is a TAB character.

	echo "contig;phylum" > header.txt
	cat header.txt ctg_tax.txt | cut -f1 -d";" > ctgs.txt
	cat header.txt ctg_tax.txt | cut -f2 -d";" | paste ctgs.txt - > G7mega5_tax.txt
	
Join taxa assignments with coverage and %GC with that awesome custom script: Download [outer_join_tabfiles.pl](https://www.dropbox.com/s/33ys9vd9fo442vt/outer_join_tabfiles.pl?dl=1) and move the script to your working directory.
	
Open a terminal window and make the script executable
	
	cd ~/Dropbox/R_directory/Metagenomic_analyses/GC_v_Coverage/
	chmod +x outer_join_tabfiles.pl


Add taxonomy to file for the matching R dataframe from last week- G7mega5_ln_cov_gc_metabat.txt. You'll have to use the one provided to the class. [Download it here](https://www.dropbox.com/s/k3xa0nabfoqfzli/G7mega5_ln_cov_gc_metabat.txt?dl=1) and move it to your working directory


	./outer_join_tabfiles.pl G7mega5_ln_cov_gc_metabat.txt exported_tax_fastas/G7mega5_tax.txt | cut -f1-5,7 > G7mega5_ln_cov_gc_metabat_tax.txt


## R visualization
** **Note****  Many of the contigs have no taxonomy assignment, so that data is missing for those rows. When the data is imported, remember to now included `fill=TRUE` in the command.

Modify the following script for your own commands

```
# Coloring GC vs Coverage with MEGAN phylogeny results
# Last edited 5/12/17
# Jessica Blanton
# Sample = GUM007 5% megahit assembly

setwd("~/Dropbox/R_directory/Metagenomic_analyses/GC_v_Coverage")
library("ggplot2"); packageVersion("ggplot2")

# LOAD & EXPLORE YOUR DATA:----
# read in data
G7gvc_bin <- read.table("G7mega5_ln_cov_gc_metabat_tax.txt",
                        header=T, sep="\t", na.strings="NA", dec=".", strip.white=TRUE, fill = TRUE)
# check column headers
colnames(G7gvc_bin) 

# calculate depth from read coverage over the length of each sequence
depth_coverage<- ((G7gvc_bin$depth_mapped)*120)/(G7gvc_bin$length)
G7gvc_bin_cov <- cbind(G7gvc_bin,depth_coverage)

#VIZUALIZE DATA: make plots of %GC vs Depth of coverage ----

# 1. Plot all contigs
qplot(gc, depth_coverage, data=G7gvc_bin_cov, main ="Composition of all contigs", color=phylum)

# 2. Eliminate outlier high coverage AND limit to larger contigs and replot
G7gvc_bin_cov_3ku300x <- subset(G7gvc_bin_cov, depth_coverage <= 300 & length >= 3000)
qplot(gc, depth_coverage, data=G7gvc_bin_cov_3ku300x, main ="Composition of large contigs, cov outliers", color=phylum)

G7gvc_megan_3ku300x.plot <- qplot(gc, depth_coverage, data=G7gvc_bin_cov_3ku400x, main ="Composition of large contigs, cov outliers",  color=phylum)
ggsave("G7gvc_megan_3ku400x.jpeg",plot = G7gvc_megan_3ku300x.plot)

```
**Please email in your .jpeg file and look to answer the following**:

**Q.** In the G7gvc_megan_3ku300x plot, what are the dominant taxonomic groups labeled? 