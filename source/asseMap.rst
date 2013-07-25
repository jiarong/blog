##########################################################
Metagenome Assembly - thoughts on reads mapping back rate
##########################################################

:Author: Jiarong Guo
:Tags: bioinformatics, assembly, metagenomics, mapping rate
:Date: 2013-07-22
:Slug: asseMap
:Category: science

(with Adina Howe, James Tiedje, Titus Brown)

Background
==========
This is a follow-up on `another blog <http://jiarong.github.io/blog/asseSum.html>`__ post on shotgun metagenome assembly of the ARMO (Amazon Rain Forest Microbial Observatory) project. Here I will mainly discuss the problem of low raw reads mapping back rate to the assembled contigs (Table 1).

.. table:: Table 1: Assembly statistics. Minimum contig length of 800 bp is chosen. The low mapping percentage is due to the high minimum contig length cutoff.  
  

   =======  ==============  =========  =====  ========  =========  ===========
   Sample   >800bp contigs  total bp   max    mapping   mapping\%  total reads
   =======  ==============  =========  =====  ========  =========  ===========
   Forest          265073   271553890  9115   10732078  0.37\%      2923068636
   Pasture         497664   538669724  24861  23082813  0.81\%      2863547487
   =======  ==============  =========  =====  ========  =========  ===========

There are two reasons that I can not use a common explaination (excuse) "bacause it is a metagenome": 1) `Dr.Titus Brown <http://ivory.idyll.org/blog/>`__ pointed out the digital normalization output showed the significant genomic saturation (figure 3, 4 in `this post <http://jiarong.github.io/blog/asseSum.html>`__). About 25% of the overall data were thrown away by digital normalization, which means they are from genomes with more than 10 fold coverage) and should be assembled. 2) with the same methods (digital normalization and partitioning), `Dr. Adina Howe <http://adina.github.io/>`__ had acheived the between 10% - 15% raw read mapping back rate on another soil metagenome project.

Results
===================

To track down the problem, I can think of two most probable causes:

- **contig length cutoff**
- **mapping tools**
- **assembly tools**

Contig length cutoff
--------------------
It is obvious that the lower minimum contig length cutoff can give higher raw reads mapping back rate. But here I want to emphasize the relative abundance of the short contigs. Figure 1 shows the majority (>95%) of contigs are less than 1000bp and about 50% of contigs are less than 500bp when 400bp is chosen as minimum. The minimum contig length should be a key parameter to be reported together with raw read mapping rate.
In my data, 800bp minimum cutoff gives 0.37% mapping back rate, while 400bp cutoff gives 2.97%. But the mapping back rate is still low based on the portion of overall reads (25%) thrown away by digital normalization.

.. .. figure:: |filename|/images/F.contigs.400bp.fasta.lenDist.png
.. figure:: static/images/F.contigs.400bp.fasta.lenDist.png
   :width: 80%

   Figure 1: cumulative distribution of contigs based on their lengths. X axis is the length of contigs and Y axis is the number of contigs shorter than a certain length. Contigs have minimum length cutoff of 400bp.

Mapping methods
---------------
I then tested the different mapping methods. The mappers (bwa-aln, bowtie2-e2e, bowtie) requiring the whole read mapped (global alignment) should have lower mapping rate than those (bwa-mem and bowtie2-local) allowing partial match (local alignment), which is confirmed by my testing results (Table 2). Bowtie2-e2e gave higher mapping rate than bowtie due to allowing mismatches. Bwa-aln only allowed a certain number or percentage of mismatch (1% in my case), while bowtie2-e2e took any hit that has higher score than minimum, which is less stringent.

.. Table:: Table 2: Comparison of the different mapping methods. F is all the reads of the forest soil metagenome. Flump is a the biggest partition of that sample. The reads are mapped the contigs with 400bp minimum cutoff.

    =====  =======  =======  ===========  =============  ======
    data   bwa-aln  bwa-mem  bowtie2-e2e  bowtie2-local  bowtie
    =====  =======  =======  ===========  =============  ======
    Flump    10.1%    35.1%        14.5%          31.4%    7.5%
    F         2.2%     8.0%         3.3%           6.8%     N/A
    =====  =======  =======  ===========  =============  ======

Assembly methods on partitions
------------------------------
After digital normalization and partition, there is no consensus on best way (assembler) to assemble individual partitions. I previously tried a range of Ks and picked the assembly with most assembled basepairs. By merging different assembly from K, I got significantly more contigs (Table 3), which suggests each partition still consists of organisms with different coverage.

.. Table:: Table 3: Assembly statistics of multiple K merging method and best K method applied to individual partitions of the forest soil metagenome.

    =============  ==============  ==========  =====
    method         >400bp contigs  total bp    max
    =============  ==============  ==========  =====
    multiK merged        13594068  7722481956  17000
    bestK                 3916562  2124266385   9115
    =============  ==============  ==========  =====

Improved reads mapping back rate
--------------------------------
The multiple K merged assembly with 300bp minimum cutoff had **6.75%** reads mapped with bwa-aln and **22.80%** with bwa-mem. The differece of bwa-aln and bwa-mem shows about 17% of reads are partially mapped to the assembly, which indicates the assembly is highly chimeric.

Now lets go back to the two original two problems:

1. 25% reads thrown away by the digital normalization process should be from speices with good coverage (> 10).  

   The bwa-mem shows 22.80% of reads contributing to the assembly and the number (22.80%) is close to 25%. However, the minimum length chosen here is 300bp. If longer minimum length was chosen, the mapping rate would be very low. Thus there is still room for improvement to get longer contigs. 

2. low mapping back rate compared to the other project (10% -15%).

   First, the metagenome yielding 10% -15% read mapping rate has very similar size to mine, but mine is pooled data from five different location so probably lower coverage. Second, I used bwa-aln instead of bowtie2-e2e. As shown in Table 2, bowtie2-e2e gave higher mapping rate than bwa-aln.

Conclusion
==========

The read mapping back rate is an important parameter for evaluating the quality of metagenome assembly and deciding whether sequencing depth is enough for assembly. Summary of the points learnt:

- Minimum length cutoff and read mapping tool can change the mapping rate a lot, so it is important to check these two parameter when comparing the read mapping back rate. 
- Unlike single genome assemlby, merging multiple K can improve metagenome assembly a lot.
- The mapping rate difference between bwa-mem and bwa-aln can be an indicator for chimeric level of the assembly.


Methods
=======

For assembly tool, velvet (velveth and velvetg) was used with default parameters. Multiple K from 29 to 69 with a step of 4 were chosen except for the largest partition. A step of 10 was used for the largest partition. SGA (fm-merge) is used for merging the assemblies.

For mapping tools, bwa-aln allowed 0.01 mismatch (bwa aln -n 0.01) and bowtie allow 2 mismatch (bowtie -S -v 2). Bwa-mem (bwa mem), Bowtie2-e2e (bowtie2 --end-to-end) and bowtie2-local (bowtie2 --local) used default parameters.

