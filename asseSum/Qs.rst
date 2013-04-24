::

  Jiarong, Very nice to get this assembly done on BIG data, even though it
  seems still hugely diverse and most unassembled.  I have a few questions:

  What is the k and c for digital normalization. I know one number is the
  required number before the identical ones are set aside, but which and what
  is the other number? the length required for the repeat?

"k" is the size for kmer (the length required for the repeat) and "c" is fold coverage cutoff (the required number before the identical ones are set aside).

::

  The distribution of cpu time seems different than Adina had for the Great
  Prairie, i.e. she had much less for digital normalization and much more for
  artifact removal. Is this due to improvements in the algorithms, data size or
  does it have some biological meaning?

I just checked Adina's poster. The Iowa corn sample has 1.8 billion reads. The amazon forest or pasture samples are about 3 billion reads after preprocess. So digital normalization on my data should take longer time (at least half more). The actifact removal time depends on how much reads left after digital normalization, but generally should be less than digital normalization. Adina's assembly process is a little different. The "artifact removal" you mentioned might be "delump", which is very time consuming. I did not do delump for my biggest partition but just assembled it with big memory. In sum, there is no change in algorithm and biological meaning we can get out of the running time, I think.


::

  How much data was removed with the artifact removal, and with the remaining
  was the portion assembled the same as for the non-lump?

For forest data, 1/4 is remove digital normalization, and 15% removed by artifact removal.

::

  Why the big hump in the forest saturation curve after 1.5. Which sample is
  it, #6,7,8?  Maybe there is something about the sites that can explain it?

Those are the data from Forest 10 meter square (F10). From figure 6 (the heatmap), we can see F10 is most different. I do not know why, maybe people did the soil sampling have some clues.

::

  So 9.3% of the forest contigs were covered by pasture ones, and 3.5% of the
  forest bp covered by ones from pasture? So more of forest history is seem in
  pasture sequence? though the overall amount is  very low. For contig
  coverage, how long and how perfect is the match requirement?

The amount of basepairs shared between forest and pasture take up larger portion in pasture just because pasture assembly is smaller. My main question here is *why the forest assembly is smaller?* The conclusion I get here is that the DNA content change a lot after land conversion based on the < 5% similarity. If we want to fit in the "forest history shown in pasture sequence" story, the forest assembly should be close to a subset of pasture.

::

  Since pasture sequences have more in common ninth each other, why does the
  forest heat map show more darker blue? Are their very dominant sequences more
  in common, and that is not apparent in Fig. 5  because it merges with the
  left axis? Or?

::

  The MG-RAST for 800bp should be more useful, at least than for using if for
  the short reads as is often done, It will be interesting thought to see how
  much beyond housekeeping is seen?? Also  to see if genes of unknown taxa are
  common with each other (hence dominant clades though unknown taxa).
