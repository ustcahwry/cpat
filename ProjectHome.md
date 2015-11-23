# Introduction #
Using RNA-seq, tens of thousands of novel transcripts and isoforms have been identified (Djebali, et al Nature, 2012 , Carbili et al, Gene & Development, 2011) The discovery of these hidden transcriptome rejuvenate the need of distinguishing coding and noncoding RNA. However, Most previous coding potential prediction methods heavily rely on alignment, either pairwise alignment to search for protein evidence or multiple alignments to calculate phylogenetic conservation score (such as CPC , PhyloCSF and RNACode ). This is because most previously identified transcripts including protein coding RNA and short, housekeeping/regulatory RNAs such as snRNAs, snoRNA and tRNA are highly conserved. While still very useful, these approaches have several limitations:

  * Most lncRNAs are less conserved and tend to be lineage specific which greatly limit the discrimination power of alignment-based methods. For example, of 550 lncRNAs detected from zebrafish, only 29 of them had detectable sequence similarity with putative mammalian orthologs (Ulitsky et al, Cell, 2011).
  * A significant fraction of protein coding genes may have an alternatively processed isoform or one transcribed from an alternative promoter, these part of ncRNA cannot be correctly classified through homologous search because they would have significant match to protein coding genes.
  * Alignment based method is extremely slow. For example, CPC takes 6050 CPU minutes (> 4 days) to evaluate 14,000 lncRNA transcripts.
  * Reliability depends on alignment quality. Most multi-alignment tools use heuristic search and do not guarantee to give optimal alignments.

CPAT overcomes the above issues by using logistic regression model based on 4 pure sequence-based, linguistic features

  * ORF size
  * ORF coverage
  * Fickett TESTCODE
  * Hexamer usage bias

Linguistic features based method does not require other genomes or protein databases to perform alignment and is more robust. Because it is alignment free, it runs much faster and also easier to use. For example, CPAT only took several minutes to evaluate the above 14,000 lncRNAs. More importantly, compared with alignment-based approaches, CPAT achieves better sensitivity and specificity (0.966 tested on human gene annotation).

# Documentation #
http://rna-cpat.sourceforge.net

# Online webserver #
http://lilab.research.bcm.edu/cpat/

# Code #
https://sourceforge.net/projects/rna-cpat/files/?source=navbar




**Contacts:**

  * **Liguo Wang**: wang.liguo@mayo.edu
  * **Hyun Jung Park**: hjpark@bcm.edu
  * **Wei Li**: wl1@bcm.edu

**Reference**

Wang, L., Park, H. J., Dasari, S., Wang, S., Kocher, J.-P., & Li, W. (2013). CPAT: Coding-Potential Assessment Tool using an alignment-free logistic regression model. Nucleic acids research. doi:10.1093/nar/gkt006

