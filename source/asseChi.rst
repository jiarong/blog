###########################################
Metagenome assembly - Are the contigs real?
###########################################

:Author: Jiarong Guo
:Tags: Metagenomics, Assembly, Chimeric contig
:Date: 2013-07-19
:Slug: asseChi
:Category: Science

with Adina Howe, James Tiedje, and Titus Brown

Background
==========

This is one of my assembly series. In my other post about reads mapping back rate, I found more reads only partially mapped to the contigs (about 17%) than those entirely amapped (about 6%), which suggested the *de brujin* graph assembler used (velvet) was putting wrong piecies together and creating chimeric contigs (Figure X). This results led me to think about how to identify these chimeric contigs.

Two purposes of the post:

- Identify chimeric contigs.
- Make people aware of the amount of chimeric contigs

Figure X. Two read graph cross at one kmer.

Analysis
========



Conclusion
==========

The metagenome assembly has much room to improve, especially in terms of getting long contigs, given the fact that some members has already > 10 fold coverage. The chimeric contigs are the second to worry about but necessary.
