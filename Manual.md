

# <font color='red'> Most recent document is available <a href='http://dldcc-web.brc.bcm.edu/lilab/liguow/CGI/cpat/_build/html/index.html'>here</a></font> #

# <font color='blue'>Introduction </font> #
Deep transcriptome sequencing (RNA-seq) provides unprecedented opportunities to interrogate the whole transcriptome. Using RNA-seq, tens of thousands of novel transcripts and isoforms have been identified; therefore, the need of coding potential prediction has been rejuvenated. We present CPAT (Coding Potential Assessment Tool), a program to discriminate coding and noncoding genes using logistic regression model based on 4 selected features. It is much faster, more accurate and convenient to use than other available tools.

Fetures used to predict coding potential:
  * mRNA size
  * putative (maximum) open reading frame (ORF)
  * [Fickett TESTCODE](http://www.ncbi.nlm.nih.gov/pubmed/7145702) statistics
  * [phastCon](http://compgen.bscb.cornell.edu/phast/phastCons-HOWTO.html#paper) conservation score

# <font color='blue'>Installation</font> #
  * prequisites
    1. [python2.7](http://www.python.org/getit/releases/2.7/)
    1. [numpy](http://numpy.scipy.org/)
    1. [R](http://www.r-project.org/)
  * If your computer can not connnect to internet, nose>= 0.10.4 and distribute-0.6.10 are also required.
  * Install CPAT on MAC OSX, Mac users need to download and install Xcode beforehand.
  * If the installation failed with error like: `/usr/bin/ld: cannot find -lz`, you may need to install a shared [zlib](http://zlib.net/) library on your system.


```
1. tar zxf CPAT-VERSION.tar.gz
2. cd CPAT-VERSION
3. python setup.py install #will install CPAT in system level. require root previledge
4. python setup.py install --root=/home/user/CPAT #will install CPAT at user specified location
5. export PYTHONPATH=/home/user/CPAT/usr/local/lib/python2.7/site-packages:$PYTHONPATH. #setup PYTHONPATH
6. export PATH=/home/user/CPAT/usr/local/bin:$PATH #setup PATH

```

# <font color='blue'>Input file format</font> #
  * [FASTA](http://en.wikipedia.org/wiki/FASTA_format) file is used to represent transcript sequence or reference genome sequences.
  * [BED](http://genome.ucsc.edu/FAQ/FAQformat.html#format1) file is tab separated, 12 column, plain text file to represent gene model. Here is an [example](http://dldcc-web.brc.bcm.edu/lilab/liguow/RSeQC/dat/sample.bed). Here BED file is used to represent gene structure of novel transcripts/isoforms.
  * **BigWig** is indexed, binary format of [wiggle](http://genome.ucsc.edu/goldenPath/help/wiggle.html) file. Here BigWig file is used to represent phastCon conservation score. Users can download our bigwig file from our [homepage](http://code.google.com/p/cpat/)
  * RData file is R object containing trained logit model. (Training models for Human, Mouse and Zebrafish have already been included in the tar ball)


# <font color='blue'>Runing mode</font> #
CPAT has two running modes depending the format of input novel transcripts
  * if novel transcripts is prepared in fasta format. Users do NOT need to specify reference genome (`-r`) and phastCon score (`-c`). In this scenario, only the first 3 features will be used to predict the coding potential. This mode is useful if the novel transcripts were constructed through de novo assembly but the reference genome is unknown. But still, CPAT require a training model, which can be obtained from some well studied species that has close evolution distance to it. Keep in mind that RNA sequences should be in direction of 5'->3'.
  * if novel transcripts is prepared in BED format. Users must also provide reference genome file(used to retrieve mRNA sequnece) and phastCon score (used to retrieve conservation score). In this scenario, all 4 features will be used to do coding potential prediction.

```
#run CPAT using mRNA sequence. mRNA sequence must be prepared from 5' to 3'
cpat.py -d ../dat/Human_training.RData -g test.hg19.fa  -o test.cpat

#output
head -10 test.cpat

mRNA_size       maxORF_size     fickett_score   coding_prob
NM_001001740.2  2729    2124    0.857   0.999999999998691
NM_001003679.3  5142    2691    0.6017  1
NM_001003680.3  3079    2877    0.5522  1
NM_001006605.4  2603    1287    0.6397  0.999998833159587
NM_001017415.1  3519    2358    0.987   1
NM_001017416.1  3424    2358    0.987   1
NM_001020658.1  5416    3567    1.1077  1
NM_001033581.1  2147    1230    1.3191  0.999999896243908
NM_001033582.1  2044    1230    1.3191  0.999999896164363

#run CPAT using BED file. Reference genome (fasta format) and conservation score (bigwig format) must be provided.
cpat.py -d ../dat/Human_training.RData -g test.hg19.bed -r hg19.fa -c hg19.phastCon.46way.vertebrates.bw -o test.cpat

#output
head -10 test.cpat

mRNA_size       maxORF_size     fickett_score   phastCon_score  coding_prob
NM_032291       4694    2487    0.9574  0.716472793144  1
NM_001145278    2003    714     0.9206  0.413123814035  0.998849763874739
NM_001145277    2005    822     1.0235  0.416249324199  0.999825657384658
NM_052998       2182    1383    1.2347  0.778123109085  0.999999994911166
NM_001080397    2401    2247    1.1547  0.805272990095  1
NM_032785       2988    1512    0.9756  0.650110288158  0.999999995606068
NM_018090       2081    792     0.9283  0.433011466984  0.999656333515736
NM_013943       4434    762     1.0759  0.33868611929   0.999619071644848
NM_001195684    6300    2553    1.0555  0.524233320933  1

```


# <font color='blue'>Performance</font> #
## <font color='blue'>10-fodl cross validation (Prediction Power)</font> ##
![https://sites.google.com/site/liguowangspublicsite/home/Untitled-1-01.png](https://sites.google.com/site/liguowangspublicsite/home/Untitled-1-01.png)

We select 10,000 coding gene and 10,000 noncoding genes from Human gene annotation database such as Refseq and GenCode. 10-fold cross validations were performed to estimate CPAT's prediction performance.
(A) ROC (Receiver operating characteristic) curve of CPAT when all 4 features were used as predict variables. Dashed blue line represent 10 round of validations, solid red curve indicates average trend. (B) ROC curve of CPAT when only 3 sequence features (mRNA size, ORF size, Fickett TESTCODE statistic) were used as predicting variables. Dashed blue line represent 10 round of validations, solid red curve indicates average trend. (C) Relationship between PPV (Positive Predictive Value) and TPR (True Positive Rate). Dashed blue curve represents 10 round of validations, solid blue curve indicates average trend. (D) Overlay sensitivity (blue) and specificity (red) curve. As there is always a trade off between sensitivity and specificity. The junction point of two curves indicates a optimum cutoff (0.375) to compromise sensitivity and specificity. When this cutoff was applied, both sensitivity and specificity reaches 0.96.

## <font color='blue'>Compared with CPC using independent dataset</font> ##
We choose [14,353 human lincRNAs](http://www.broadinstitute.org/genome_bio/human_lincrnas/sites/default/files/lincRNA_catalog/lincRNAs_transcripts.bed) identified by Broad Institute using RNA-seq data generated from [Human Body Map project ](http://www.broadinstitute.org/genome_bio/human_lincrnas/) as independent test dataset because 1) It is public available so that any one can download and repeat our comparison, and 2) the RNA-seq data was sequenced very deep (4 billion reads) and comprehensive (24 human tissues), so this set of lincRNA should be very reliable.

### Running Speed Comparison ###
Strictly speaking, this is not a direct comparison, because number of CPU are different. We used 2 CPU simply because CPC is so slow. In general, CPAT is 100x ~ 1000x times faster than CPC.
  * running speed for [CPC](http://cpc.cbi.pku.edu.cn/) (using 2 CPU)
    * real:    3,056m
    * user:    6,050m (the amount of CPU time)
    * sys:    28m
  * running speed for CPAT (using 1 CPU)
    * real:    12m
    * user:    6m (the amount of CPU time)
    * sys:    0m21s
### Specificity Comparision ###
In Figure below. Black solid line is cumulative curve of coding probability (CPAT) or CPC score. Red dashed lines are cutoffs. Overall, 93% of lincRNA were identified as noncoding by CPAT (using cutoff 0.375), and 76% of lincRNA were identified as noncoding by CPC.

<img src='https://sites.google.com/site/liguowangspublicsite/home/CPAT.png' width='300' height='300'>
<img src='https://sites.google.com/site/liguowangspublicsite/home/broad_lincRNA_CPC.png' width='300' height='300'>

<h1><font color='blue'>How to choose cutoff</font></h1>
Based on our 10-fold cross validation, we recommend the following cutoff to use:<br>
<ul><li>Human and Mouse: 0.375<br>
</li><li>Zebra fish: 0.40