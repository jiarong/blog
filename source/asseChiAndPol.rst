############################################################
Metagenome assembly - untangling polymorphism and chimerism
############################################################

:Author: Jiarong Guo
:Tags: Metagenomics, Assembly, Chimerism, Polymorphism
:Date: 2013-07-30
:Slug: asseChiAndPol
:Category: Science

With Adina Howe, James Tiedje, and Titus Brown

Background
==========

This is a follow-up on my other post on `contig chimerism <https://github.com/jiarong/blog/blob/master/source/asseChi.rst>`__. In that post, I mainly discussed large portion of read level chimeric contigs from *de brujin* graph assemblers and methods to identify them by using local and global alignment (relative to reads) mapping tools. Here I will discuss more details in the context of polymorphism, as suggested in digital normalization process. There are two main questions:

- What's the relation between polymorphism and chimerism?
- How to explain the big mapping rate difference using local and global mappers?

Results
=======

.. table:: Table 1. Mapping statistics. Contigs are counted as covered if > 95% are covered. SNP percentage is calculated as total SNP/total alignment.

        ===================  ============  ===============  ======
        methods              reads mapped  contigs covered  SNP
        ===================  ============  ===============  ======
        bowtie (-v 0 -a)           18.46%            23.7%  0.000%
        bowtie (-v 1 -a)           25.91%            36.1%  0.220%
        bowtie (-v 2 -a)           30.36%            42.4%  0.440%
        bowtie (-v 3 -a)           33.83%            46.9%  0.710%
        bowtie2 (-a)               50.50%            52.1%  3.710%
        bowtie2 (-a –local)        73.87%            62.1%  6.000%
        ===================  ============  ===============  ======

In the contig coverge analysis here, we want all alignments of a reads that fullfill a certain requirement. Since bwa-aln only output one primary alignment per read and bowtie provides more output options, we choose bowtie or bowtie2 to do the mapping.

.. figure:: static/images/readChi.png
   :width: 80%

   Figure 1. *De brujin* graph of two reads sharing one kmer in the middle. Path c -> e -> b and a -> e -> d result in read level chimera.

The strict definition of read level chimeric contigs is a contig with a region where no read can fully mapped. As shown in Table 1 (bowtie with no mismatch), most of the contigs (76.3%) are chimeric according to this definition. When more mismatches are allowed, more reads are mapped and more contigs get >95% covered. Bowtie2-e2e does not do maximum mismatch setup but do the alignment score (minimum of 30 by default), so it is more able to find more dissimilar full length match. Using bowtie2-e2e, 52.1% of contigs are >95% covered and 50.5% reads are mapped. The difference of mapping statitics between bowtie with no mismatch and bowtie2 indicates many (32.04%) of reads are polymorphic to their aligned region in contigs and additional 28.34% of contigs could be covered by very similar full length reads. Even though many contigs have read level chimeric regions, those regions are very similar to some reads that can be fully aligned to them, so these contigs are still useful as assembled contigs. 

Here is an example for the above analysis: In Figure 1, if the assembly path is c -> e -> b or a -> e -> d, both read1 and read2 can not be mapped to the contigs without any mismatch, so c -> e -> b and a -> e -> d are read level chimeras with strict definition. However, because read1 and read2 are very similar, a chimeric contig assembled using parts from both is still useful for further mapping and annotion, like a consensus sequence. Global alignment mappers (bwa-aln and bowtie2-e2e) can still recognize the reads as hit to the contigs.

On the other hand, if two reads are not similar (not polymorphic) and they just share a kmer in the middle, chimeric contigs in this case can potentially have defferent annotation and should be avoided. They can not be aligned with global alignment mapper but can be aligned with local alignment mapper, and can be further identified and removed using this characteristic.

Which mapper should used for mapping reads back to assembly?
============================================================

It depends on the purpose:

- For checking read level chimerism, global alignment mapper are better (bwa-aln, bowtie and bowtie2-e2e), so contigs not well covered are chimeric as discussed above and `this post <https://github.com/jiarong/blog/blob/master/source/asseChi.rst>`__.

- For checking SNP and number of reads contributed to the assembly, local alignment mappers such as bwa-mem and bowtie2-local are better suited, because are more sensitive to find similar sequences (homolog or paralog), no matter the read is fully aligned or not. Further, considering the digital normalization process, these reads (mapped by local alignment mappers) are contributing the median kmer count of each other. Thus when we look for the consistency of redundancy suggested by digital normalization and by read mapping back rate, local alignment mappers are more reasonable choice (In the `ARMO assembly <https://github.com/jiarong/blog/blob/master/source/asseMap.rst>`__, the percentage of reads thrown away by digital normalization (25%) and percentage of reads mapped back (22%) are close.


How to explain the big mapping rate difference using local and global mappers?
==============================================================================
Mostly explained in the above question. In addition, read level chimeric contigs can only be aligned by local alignment mapper.

What's the relation between polymorphism and chimerism?
=======================================================
Polymorphism can cause chimerism. If a DNA region is high polymorphic, the *de brujin* graph built from variants of that region could be very complex, the final path picked might chimeric at read level (Figure 1). However, the contig assembled is still useful and representative of that DNA region. I donot think there is golden standard to tell whether a read level chimera is useful or not, but treating contigs not well covered by global alignment mappers as bad chimeric contigs is reasonable.

Conclusions:
===========

A quick summary:

- Local alignment tools (bwa-mem and bowtie2-local) are better for SNP analysis and counting reads contributing to assembly; while global alignment mappers should be use to identify the read level chimeras.

- Polymorphism can cause chimera, but if DNA variants are highly similar, the chimeric contigs is still useful. 

