# Using Machine Learning to predict Anti-Microbial Resistance

# Salko Hrnjic

# Background

Bacteria develop anti\-microbial resistance \(AMR\) due to mutations in their genetic code\, which protects them against certain molecular compounds\, such as antibiotics\.

This is a relevant issue in the medical field due to the difficulty of treating infections involving resistant bacteria\.

Resistance to drugs can be isolated to specific genes\, which are highly conserved through bacterial replication\.

Predicting AMR using machine learning provides an opportunity to support medical professionals in patient care\.

# Data

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_0.png)

AMR genotypes and phenotypes retrieved from Bacterial and Viral Bioinformatics Resource Center\(BV\-BRC\)

Currently\, classifiers have been developed for multiple genomes already\. Many use AdaBoost\.

Salmonella enterica and resistance to antibiotic ampicillin were chosen\.

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_1.png)

121 genomes selected with attached contig files \(genetic sequence data that shows a “contiguous” map of the gene strain\.

Use BV\-BRC API to retrieve FASTA files\, and SEQTK to convert them into FASTQ\.

Split genomes depending on AMR phenotype \(resistant or susceptible\)\.

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_2.png)

# K-Mer Pre-processing

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_3.png)

K\-mers are substrings of fixed length within a DNA/RNA sequence\.

Each Contig file for each genome must be processed\.  Due to the large volume of input data\, a publicly available K\-mer counter \(KMC\) is used \(Citation 1\)\.

# Finding most informative K-mers

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_4.png)

K\-mer occurrences are merged into a single matrix for each genome\. Each row represents an individual K\-mer and the columns depict a 1 or 0 depending on the presence of that K\-mer in a given genome\.

K\-mers that indicate resistance will be present in resistant strains of salmonella and absent from the susceptible strains\.

Columns that correspond to susceptible genomes have their values inverted\, so that the most notable K\-mer will be in the row with the fewest zeros\.

This matrix is then passed to an AdaBoost algorithm with the goal of finding the most informative K\-mers

# The adaboost algorithm

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_5.png)

The initial part of the algorithm is seeking optimal K\-mers

The first array of values corresponds to a given K\-Mer\, and the second contains probability values for each matrix column\.

In the first round\, each element in the probability array is set to 1/n \(where is n the number of genomes\)

# ADAboost Algorithm

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_6.png)

# Adaboost Algorithm

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_7.png)

Each element in the probability array is now updated based on the corresponding matrix index for the best K\-mer\. If the corresponding index position was a miss\, then the probability is updated according to equation 2\. Otherwise\, it is updated according to Equation 3:

Next\,  the unnormalized probabilities are normalized at each index by dividing by the sum of unnormalized probabilities for each element in the probability array\, according to equation 4:

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_8.png)

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_9.png)

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_10.png)

<span style="color:#ffffff">With the best K\-mers found\, they can be used as a classifier for AMR\. The genomes are split with 80% of them being used for training\, and the last 20% intended for testing\.</span>

<span style="color:#ffffff">Due to the computational intensity of processing all K\-mer occurrences for the initial part of the algorithm\, processing the data at the full scope outlined by David et al was not feasible\. A test with synthetic data found that it would take 3\-5 hours for the entire algorithm to finish\, assuming no kernel crashes\.</span>

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_11.png)

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_12.png)

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_13.png)

A subset of the data was used and one best K\-mer was found\, which was then used to classify genomes based on the presence of that K\-Mer alone\.

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_14.png)

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_15.png)

# AMR Prediction

The classifier will predict AMR based on the K\-mer across all genomes and then test against the actual phenotypes\.

![](img/Predicting%20Anti-Microbial%20Resistance%20with%20Machine%20Learning_16.png)

# performance with One K-MeR

# References

<span style="color:#2a2a2a">Marek Kokot\, Maciej Długosz\, Sebastian Deorowicz\, KMC 3: counting and manipulating </span>  <span style="color:#2a2a2a"> _k_ </span>  <span style="color:#2a2a2a">\-mer statistics\, </span>  <span style="color:#2a2a2a"> _Bioinformatics_ </span>  <span style="color:#2a2a2a">\, Volume 33\, Issue 17\, September 2017\, Pages 2759–2761\, </span>  <span style="color:#006fb7">[https://doi\.org/10\.1093/bioinformatics/btx304](https://doi.org/10.1093/bioinformatics/btx304)</span>

<span style="color:#222222">Davis\, J\.\, Boisvert\, S\.\, Brettin\, T\. </span>  <span style="color:#222222"> _et al\._ </span>  <span style="color:#222222"> Antimicrobial Resistance Prediction in PATRIC and RAST\. </span>  <span style="color:#222222"> _Sci Rep_ </span>  <span style="color:#222222"> </span>  <span style="color:#222222"> __6__ </span>  <span style="color:#222222">\, 27930 \(2016\)\. </span>  <span style="color:#222222">[https://doi\.org/10\.1038/srep27930](https://doi.org/10.1038/srep27930)</span>

Sebastian Deorowicz\, Marek Kokot\, Szymon Grabowski\, Agnieszka Debudaj\-Grabysz\, C 2: fast and resource\-frugal k\-mer counting\, Bioinformatics\, Volume 31\, Issue 10\, 15 May 2015\, Pages 1569–1576\, [https://doi\.org/10\.1093/bioinformatics/btv022](https://doi.org/10.1093/bioinformatics/btv022)

Deorowicz\, S\.\, Debudaj\-Grabysz\, A\. & Grabowski\, S\. Disk\-based k\-mer counting on a PC\. BMC Bioinformatics 14\, 160 \(2013\)\. [https://doi\.org/10\.1186/1471\-2105\-14\-160](https://doi.org/10.1186/1471-2105-14-160)

