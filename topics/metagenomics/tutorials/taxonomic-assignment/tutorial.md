# Taxonomic Assignment Tutorial
---
layout: tutorial_hands_on
 
title: Taxonomic Assignment

zenodo_link: still needs to be created

questions:
- Which species are present in my sample?

objectives:
At the end of this course, learners will be able to:
- explain how taxonomic assignment works
- apply Kraken2 to assign taxonomic labels
- apply Krona to visualize results of assignment and understand the output
- identify taxonomic classification tool that fits best depending on their data

time_estimation: 45 min

contributors:
- Sophia
 
---
# Introduction
 
Microbiome data can be gathered from different environments such as soil, water or the human gut. The biological interest lies in general in the question how the microbiome present at a specific site influences this environment. Metagenomic samples contain DNA from different organisms at a specific site, where the sample was collected. Metagenomic data can be used to find out which organisms coexist in that niche and which genes are present in the different organisms. Metatranscriptomic samples include the transcribed gene products, thus RNA, that therefore allow to not only study the presence of genes but additionally their expression in the given environment. The following tutorial will focus on metagenomics data, but the principle is the same for metatranscriptomics data.
The investigation of co-occurring and thus potentially interacting microbes present at a specific site and their relative abundance is also called “microbial community profiling”.
Basic for this is to find out which microbes are present in the sample. This can be achieved for all known microbes, where the DNA sequence specific for a certain species is known. The process of sorting single DNA reads derived from sequencing to a species (or other ranks) is called taxonomic assignment. In general, taxonomy is the study of sorting organisms into different groups within a larger system according to similarities and differences. The groups are named, defined, classified and hierarchically ordered. The principal ranks from top to bottom (*with examples for the human being*) are domain (*eukarya*), kingdom (*animalia*), phylum (*chordata*), class (*mammalia*), order (*primates*), family (*hominidae*), genus (*homo*), and species (*sapiens*). From this classification, one can generate a tree of life, also known as a phylogenetic tree. It is a rooted tree that describes the relationship of all life on earth. At the root sits the “last universal common ancestor” and the three main branches (in taxonomy also called domains) are bacteria, archaea and eukaryotes. Most important for this is the idea that all life on earth is derived from a common ancestor and therefore when comparing two species, you will -sooner or later- find a common ancestor for all of them.
 
When we talk about metagenomic data here, what we start with is sequences derived from DNA fragments that could be isolated from the sample of interest. Ideally, from all microbes present in the sample, we would also find DNA. The underlying idea of taxonomic assignment is to compare the DNA sequences found in the sample (reads) to DNA sequences of a database. When a read matches a database DNA sequence of a known microbe, we can derive a list with microbes present in the sample.
The comparison of reads to database sequences can be done in different ways, leading to three different types of taxonomic assignment.
 
## Three types of taxonomic assignment
 
For the **k-mer based** analysis, databases as well as the samples DNA are broken into k-mers about 30 bp length for comparison. From all the genomes in the database, where a specific k-mer is found, a lowest common ancestor (LCA) tree is derived and the abundance of k-mers within the tree is counted. This is the basis for a root-to-leaf path calculation, where the path with the highest score is used for classification of the sample. By counting the abundance of k-mers, also an estimation of relative abundance of taxa is possible. The major advantage of k-mer based analysis is the low compute cost. Major disadvantages are the low detection accuracy, that the unclassified percentage is unknown and that there is no gene detection, no SNVs detection and no genomic comparison possible. An example for a k-mer based analysis tool is Kraken2, which will be used in this tutorial
 
![Kraken functionality](../../images/taxonomic-assignment/Kraken_algorithm.PNG "Kraken functionality.")  xxx citation
 
For the **gene based** analysis, reads about 150 bp length are aligned to reference genes about 1 kbp length. Next, marker genes are used to estimate species abundance. Furthermore, genes can be analyzed in isolation for presence or absence in a specific condition.
The major advantage is the detection of the pangenome (entire set of genes within a species). Major disadvantages are the high compute cost, low detection accuracy and that the unclassified percentage is unknown. At least intragenic SNVs can be detected and low-resolution genomic comparison is possible.
 
For the **genome based** analysis, read pairs of 150 bp length are aligned to reference genomes of about 3 Mbp length. Considering the coverage and breadth, genomes are used to measure genome abundance. Furthermore, genes can be analyzed in genomic context. Advantages of this method are the high detection accuracy, that the unclassified percentage is known, that all SNVs can be detected and that high-resolution genomic comparisons are possible. This method takes medium compute cost.
 
After this theoretical introduction, let's now get hands on analyzing an actual dataset! 

> ### Agenda
>
> In this tutorial, we will deal with:
>
> 1. TOC
> {:toc}
>
{: .agenda}
 
# Background on data
 
The dataset we will use for this tutorial comes from an oasis in the mexican desert called Cuatro Ciénegas, that is studied because of its special environmental conditions. It is paired-end data with JP4D_R1.fastq.gz being the forward reads and JP4D_R2.fastq.gz being the reverse reads. Additionally, the reads have been trimmed using __cutadapt__ (s. xxx tutorial)

## Hands on: Importing the data

> ### {% icon hands_on %} Hands-on: Data upload
>
> 1. Import the following files from [Zenodo](zenodo link)
>    - `JP4D_R1.fastq.gz`
>    - `JP4D_R2.fastq.gz`
>
>
>    > ### {% icon tip %} Tip: Importing data via links
>    >
>    > * Copy the link location
>    > * Open the Galaxy Upload Manager
>    > * Select **Paste/Fetch Data**
>    > * Paste the link into the text field
>    > * Press **Start**
>    {: .tip}
 
# Hands on: Run Kraken2 for taxonomic assignment

Our input data is the DNA reads of microbes present at Cuatro Ciénegas. To find out which microbes are present, we will compare the reads of the sample to sequences of known microbes stored in a database using the algorithm Kraken2, which is k-mer based. For this tutorial, we will use the Standard plus protozoa & fungi (2021) database.
 
> ### {% icon hands_on %} Hands-on: Assign taxonomic labels with Kraken2
>
> 1. {% tool [Kraken2](toolshed.g2.bx.psu.edu/repos/iuc/kraken2/kraken2/2.0.8_beta+galaxy0) %} with the following parameters:
>    - *"Single or paired reads"*: `Paired`
>    - *"Print scientific names instead of just taxids"*: `No`
>    - *"Confidence"*: `0.1`
>    - In *"Create Report"*:
>        - *"Print a report with aggregrate counts/clade to file"*: `Yes`
>        - *"Format report output like Kraken 1's kraken-mpa-report"*: `Yes`
>    - *"Select a Kraken2 database"*: `Standard plus protozoa & fungi (2021)`
>
>    > ### {% icon comment %} Comment
>    >
>    > A confidence score of 0.1 means that at least 10% of the k-mers should match entries in the database. This value can be reduced if a less restrictive taxonomic assignation is desired.
>    {: .comment}
>
{: .hands_on}
 
 
Kraken2 will create two output files called "Classification" and "Report".
 
Let's have a look at the classification file. (xxx add image) It has 5 columns:
 
1. C/U: classified/unclassified
2. Sequence ID
3. Taxonomy ID
4. Length of sequence (read1|read2 for paired reads)
5. indicates LCA mapping of each k-mer in the sequence |:| indicates end of first read, start of second read for paired reads --> example: "n k-mers assigned to taxon xxx"
 
 
![Kraken2 Classification Output](../../images/taxonomic-assignment/Kraken2_classification_screenshot.PNG "Kraken2 Classification Output.")
 
 
Let's also have a look at the report file. (xxx add image) It has 2 columns:
1. taxon name grouped into d_domain, p_phylum, c_class, o_order, f_family, g_genus, s_species
2. number of reads assigned to specific taxon
 
![Kraken2 Report Output](../../images/taxonomic-assignment/Kraken2_report_screenshot.PNG "Kraken2 Report Output.")
 
As both files are not very well readable, we will use __Krona__ to visualize the data.
 
# Analyze taxonomic assigment
 
Once we have assigned the corresponding taxa to each sequence, the next step is to properly visualize the data, for which we will use the __Krona pie chart__ tool ({% cite Ondov_2011 %}). But first, we need to convert the output generated by Kraken2 so it can be used as an input from the Krona tool.
 
## Convert output from Kraken2 so it can be used for Krona
 
__Convert Kraken__ tool is designed to translate results of the Kraken metagenomic classifier (see citations below) to the full representation of NCBI taxonomy. It does so by using Taxonomic ID field provided by Kraken. The output of this tool can be directly visualized by the Krona tool.
 
> ### {% icon hands_on %} Hands-on: Convert Kraken2 Output
>
> 1. {% tool [Convert Kraken](xxx) %} with the following parameters:
>    - *"Choose dataset to convert"*: Classification Output of Kraken2
>    - *"Select a taxonomy database"*: `2022-03-08`
>    - *"Read name"*: `column:2`
>    - *"Taxonomy ID field"*: `column:3`
>
{: .hands_on}
 
 
## Visualize the taxonomical classification with Krona
 
__Krona__ allows hierarchical data to be explored with zooming, multi-layered pie charts. With this tool, we can easily visualize the composition of the bacterial communities and compare how the populations of microorganisms are modified according to the conditions of the environment.
 
> ### {% icon hands_on %} Hands-on: Visualize data with Krona
>
> 1. {% tool [Krona pie chart](xxx) %} with the following parameters:
>    - *"Type of input data"*: `taxonomy`
>    - *"Input file"*: Output file of Convert Kraken
>
{: .hands_on}
 
 
Let's take a look at the [result](https://usegalaxy.eu/datasets/4838ba20a6d86765e92bccb62d7f6daa/display/?preview=True&dataset=0&node=0&collapse=true&color=false&depth=8&font=11&key=true). Using the search bar we can check if certain taxa are present.
 
 
> ### {% icon question %} Questions
>
> How many percent of the bacteria consists of the genus "paracoccus"?
>
>
> > ### {% icon solution %} Solution
> >
> > 6 %.
>
> {: .solution}
>
{: .question}
 
 
 
# Discussion: Choosing the right tool

When it comes to taxonomic assignment while analyzing metagenomic data, in this tutorial presented Kraken2 is not the only tool available. Several papers do benchmarking of different tools and their results are presented in the following section, with focus on tools that are available in Galaxy.
When talking about taxonomic assignment or taxonomic classification, most of the time we actually talk about two methods, that in practice are often used interchangeably: while **taxonomic binning** is the classification of individual sequence reads to reference taxa, **taxonomic profiling** refers to investigating relative abundances of taxa within a dataset but not to the classification of individual reads.
Tools for taxonomic classification can be divided into three groups. Nevertheless, all of them require a pre-computed database based on previously sequenced microbial DNA or protein sequences.
1. **DNA-to-DNA** classification tools compare sequencing reads with genomic databases of DNA sequences (Bracken, Kraken, Kraken2, MegaBLAST)
2. **DNA-to-Protein** classification tools compare sequencing reads with genomic databases of protein sequences (more computationally intensive because of analysis of all six frames of potential DNA-to amino acid translation) (DIAMOND)
3. **Marker based** classification tools use a reference database that only includes a subset of gene sequences (e.g. 16S rRNA sequence), which is quick, but introduces bias (MetaPhlAn2)
 
By using these tools for taxonomic binning, one always has to be aware of false positives due to the vast search space on the one hand (which dominantly occurs at low abundances) and of false negatives due to presently undiscovered microbial species that are not part of any database yet. When it comes to taxonomic profiling, thus investigating the abundance of specific taxa, the biggest problem is the abundance bias. It is introduced during isolation of DNA (which might work for some organisms better then for others) and by PCR duplicates during PCR amplification.
 
When benchmarking different classification tools, several metrics are used to compare their performance:
1. **Precision**: proportion of true positive species identified in the sample divided by number of total species identified by the method
2. **Recall**: proportion of true positive species divided by the number of distinct species actually in the sample
3. Precision-recall curve: each point represents the precision and recall scores at a specific abundance threshold → **area under the precision-recall curve (AUPR)**
 
4. **L2 distance**: representation of abundance profiles → how accurately the abundance of each species or genera in the resulting classification reflects the abundance of each species in the original biological sample (“ground truth”)
 
## Binning tools (“Critical Assessment of Metagenome Interpretation (CAMI) – a benchmark of metagenomics software”)
As mentioned earlier, taxonomic binning is the classification of individual sequence reads to reference taxa by grouping sequences into bins with a taxonomic label attached. All investigated binning tools have some characteristics in common:
- Small bins are not reliable
- High purity can be reached for higher ranks
- Below family level, all programs performed poorly
(either assigning very little data
or assigning more, but substantial misclassification)
- Completeness degrades at lower ranks and for low-abundant bins
- At increasing taxonomic distances to the reference, purity and completeness drop substantially
- Presence of plasmids and viral sequences doesn’t have any effect on binning performance
 
MEGAN and Kraken perform similarly, as they rely on similar algorithms but utilize different data properties.
 
## Profiling tools
 
Profilers, which are tools that investigate relative abundances of taxa within a dataset, fall into three groups depending on their performance:
1. Profilers, that correctly predict relative abundances
2. Precise profilers (suitable, when many false positives would increase cost and effort in downstream analysis)
3. Profilers with high recall (suitable for pathogen detection, when the failure of detecting an organism can have severe negative consequences)
 
However, some characteristics are common to all profilers:
- Most profilers only perform well until the family level
- Drastic decrease in performance between family and genus level, while little change between order and family level
- Fidelity of abundance estimates decreases notably when viruses and plasmids were present
- Taxonomic profilers vs profiles from taxonomic binning:
Precision and recall of the taxonomic binners were comparable to that of the profilers;
abundance estimation at higher ranks was more problematic for the binners
 
MetaPhlAn 2.0 belongs to the group of precise profilers. On the basis of the average of precision and recall, over all samples and taxonomic ranks, MetaPhlAn 2.0 performed second best of all 10 profilers tested.
 
“Critical Assessment of Metagenome Interpretation (CAMI): The second round of challenges”
 
![CAMI II software ranking](../../images/taxonomic-assignment/CAMI_software_ranking.PNG "CAMI II software ranking")
 
 
 
