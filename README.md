# ebv_jules

<div id="toc_container">
<p class="toc_title">Contents</p>
<ul class="toc_list">
  <li><a href="#First_Point_Header">1 Overview and directory layout</a></li>
<li><a href="#Second_Point_Header">2 Gathering materials and directory layout</a></li>
	<li><a href="#Third_Point_Header">3 Quality control using sickle</a></li>
	<ol><li><a href="#combining">Combining multiple runs per sample correctly</a></li>
		<li><a href="#trimming">Trimming reads with sickle</a></li></ol>
	<li><a href="#Fourth_Point_Header">4 Aligning reads using HISAT2</a></li>
	<ol><li><a href="index">Building the index</a></li>
		<a href="align">Aligning reads to the index</a></li>
</ul>
 
<h2 id="First_Point_Header">Overview and directory layout</h2>
  
Epidemiologists have found that different populations of the world manifest different long-term phenotypes from infection from the Epstein-Barr virus. Most notably that infected Westerners most often develop mononucleosis while Easterners, particularly Eastern Asians often go on to develop various forms of cancers. The hypothesis of this experiment is that various SNPs differences between Easterners and Westerners influence cellular populations of various interfering RNAs, affecting phenotype. Furthermore, Julianna Crivello, the lead investigator of this research proposes that Western SNPs cause greater production of various interfering RNAs which limit the extent of disease while Eastern SNPs cause lower production of various interfering RNAs, increasing chance of more serious disease, i.e., cancers. A variety of cell lines were cultured for this experiment, they are:
 
 
 TOTAL RNA LIBRARIES:
 (RPE stands for human retinal pigmented epithelial cell lines)
 <pre style="color: silver; background: black;">
 RPE (untreated cell lines) -- Negative control for basal cell line expression
 mi* (RPE with empty construct vector) -- Negative control for background effects of transfection or vector only)
 sh* (RPE with vector containing only small RNA portions of EBV) -- Determining effects of small RNA from EBV
 RE* (RPE with full length EBV vector) -- Determining full effects of EBV infection</pre>
 
 The following differential expression analyses will be conducted using `DESEQ2`:
 
 <pre style="color: silver; background: black;">RPE vs mi*
 RE vs RPE
 RE vs mi*
 sh* vs RPE
 sh* vs mi*
 sh* vs RE</pre>
 
 SMALL RNA LIBRARIES
 
 Daudi = Burkett's Lymphona (an EBV+ cancer)
 Hsa = lymphoblast cells (immortalized by the addition of EBV in lab)
 SNU = gastric carcino (an EBV+ cancer)
 RE (same as above)
 
 The following proteins were knocked down in each sample:
 <pre style="color: silver; background: black;">
 no treatment (negative control)    Dicer   Drosha    Ago1    Ago2    Ago3    Ago4    La</pre>
 
 For this portion of the analysis, only the various interfering RNAs will be used from each sample for the following differential expression analysis:
 
 <pre style="color: silver; background: black;">
 Daudi vs Hsa
 Daudi vs SNU
 Daudi vs RE
 Hsa vs SNU
 Hsa vs RE
 SNU vs RE</pre>
 
 <h2 id="Second_Point_Header">Gathering materials and directory layout</h2>
 
 The analysis is being performed at the location `/UCHC/PublicShare/jules/`.
 
 To begin, the EBV, small RNAs, and Spike-in fastas and gtfs were provided by Julianna. They reside at: `/UCHC/PublicShare/jules/jules_stuff`. The directory looks like:
  <pre style="color: silver; background: black;">
 EBV_NC_007605.1.fasta  ebv_spike_in.gff3  erccGenes.gtf.copy  ExiSEQ-NGS-QC-Spike-ins.fa  hg19.gtf</pre>
 
 Where `hg19.gtf` was copied from `/isg/shared/databases/alignerIndex/animal/hg19_ucsc/hg19.gtf`. Because `hg19.gtf` is a `gtf` and our EBV/Spike-In feature table is `gff3` we need to amend the `gtf.
 
 <pre style="color: silver; background: black;"> head ebv_spike_in.gff3 <strong>
NC_007605.1	.	miRNA_primary_transcript	41471	41536	.	+	.	ID=MI0001064;Alias=MI0001064;Name=ebv-mir-BHRF1-1
NC_007605.1	.	miRNA	41474	41495	.	+	.	ID=MIMAT0000995;Alias=MIMAT0000995;Name=ebv-miR-BHRF1-1;Derives_from=MI0001064
NC_007605.1	.	miRNA_primary_transcript	42848	42912	.	+	.	ID=MI0001065;Alias=MI0001065;Name=ebv-mir-BHRF1-2
NC_007605.1	.	miRNA	42853	42874	.	+	.	ID=MIMAT0000996;Alias=MIMAT0000996;Name=ebv-miR-BHRF1-2-5p;Derives_from=MI0001065
NC_007605.1	.	miRNA	42888	42909	.	+	.	ID=MIMAT0000997;Alias=MIMAT0000997;Name=ebv-miR-BHRF1-2-3p;Derives_from=MI0001065
NC_007605.1	.	miRNA_primary_transcript	42966	43030	.	+	.	ID=MI0001066;Alias=MI0001066;Name=ebv-mir-BHRF1-3
NC_007605.1	.	miRNA	42968	42989	.	+	.	ID=MIMAT0000998;Alias=MIMAT0000998;Name=ebv-miR-BHRF1-3;Derives_from=MI0001066
NC_007605.1	.	miRNA_primary_transcript	6956	7133	.	.	.	Name=ebv-mir-EBER2
NC_007605.1	.	miRNA	7100	7121	.	.	.	Name=ebv-mir-EBER2
NC_007605.1	.	miRNA_primary_transcript	139076	139154	.	+	.	ID=MI0003725;Alias=MI0003725;Name=ebv-mir-BART3   </strong>

head hg19.gtf
chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	gene_id "uc001aaa.3"; transcript_id "uc001aaa.3"; 
chr1	hg19_knownGene	exon	12613	12721	0.000000	+	.	gene_id "uc001aaa.3"; transcript_id "uc001aaa.3"; 
chr1	hg19_knownGene	exon	13221	14409	0.000000	+	.	gene_id "uc001aaa.3"; transcript_id "uc001aaa.3"; 
chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	gene_id "uc010nxr.1"; transcript_id "uc010nxr.1"; 
chr1	hg19_knownGene	exon	12646	12697	0.000000	+	.	gene_id "uc010nxr.1"; transcript_id "uc010nxr.1"; 
chr1	hg19_knownGene	exon	13221	14409	0.000000	+	.	gene_id "uc010nxr.1"; transcript_id "uc010nxr.1"; 
chr1	hg19_knownGene	start_codon	12190	12192	0.000000	+	.	gene_id "uc010nxq.1"; transcript_id "uc010nxq.1"; 
chr1	hg19_knownGene	CDS	12190	12227	0.000000	+	0	gene_id "uc010nxq.1"; transcript_id "uc010nxq.1"; 
chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	gene_id "uc010nxq.1"; transcript_id "uc010nxq.1"; 
chr1	hg19_knownGene	CDS	12595	12721	0.000000	+	1	gene_id "uc010nxq.1"; transcript_id "uc010nxq.1"; 
</pre>

We now change the `gtf` with the following codes:

 <pre style="color: silver; background: black;">
sed -i 's/gene_id /ID\=/g' hg19.gtf
sed -i 's/ transcript_id /Name\=/g' hg19.gtf 
sed -i 's/\"//g' hg19.gtf
sed 's/.$//g' hg19.gtf >> hg19.gff3
head hg19.gff3 
<strong>chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	ID=uc001aaa.3;Name=uc001aaa.3
chr1	hg19_knownGene	exon	12613	12721	0.000000	+	.	ID=uc001aaa.3;Name=uc001aaa.3
chr1	hg19_knownGene	exon	13221	14409	0.000000	+	.	ID=uc001aaa.3;Name=uc001aaa.3
chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	ID=uc010nxr.1;Name=uc010nxr.1
chr1	hg19_knownGene	exon	12646	12697	0.000000	+	.	ID=uc010nxr.1;Name=uc010nxr.1
chr1	hg19_knownGene	exon	13221	14409	0.000000	+	.	ID=uc010nxr.1;Name=uc010nxr.1
chr1	hg19_knownGene	start_codon	12190	12192	0.000000	+	.	ID=uc010nxq.1;Name=uc010nxq.1
chr1	hg19_knownGene	CDS	12190	12227	0.000000	+	0	ID=uc010nxq.1;Name=uc010nxq.1
chr1	hg19_knownGene	exon	11874	12227	0.000000	+	.	ID=uc010nxq.1;Name=uc010nxq.1
chr1	hg19_knownGene	CDS	12595	12721	0.000000	+	1	ID=uc010nxq.1;Name=uc010nxq.1</strong></pre>

We need to make sure that none of our chromosomes across organisms (semi-organismic) have the same name:
 <pre style="color: silver; background: black;">
wc -l hg19.gff3 
<strong>1461416 hg19.gff3</strong>
cut -f1 hg19.gff3 | grep -c "chr" 
<strong>1461416</strong></pre>

We see that every single identifier in `hg19.gff3` has `chr` in it. Let's see if that appears in our EBV/Spike_ins `gff3`:
 <pre style="color: silver; background: black;">
cut -f1 ebv_spike_in.gff3 | grep -c "chr"
<strong>0</strong></pre>

We are safe. Now let's combine our `gff3s` and `fastas`
<pre style="color: silver; background: black;">
cat ebv_spike_in.gff3 hg19.gff3 >> all.gff3
cat /isg/shared/databases/alignerIndex/animal/hg19_ucsc/hg19.fa ExiSEQ-NGS-QC-Spike-ins.fa EBV_NC_007605.1.fasta >> all.fasta
</pre>

Now let's check that every identifier in our `gff3` appears in our fasta:

<pre style="color: silver; background: black;">
cut -f1 all.gff3 >> identifiers
sort -u identifiers >> unique_identifiers
grep -wFf unique_identifiers all.fasta >> fasta_headers
sort -u fasta_headers >> unique_fasta_headers
wc -l unique
<strong>113 unique_fasta_headers
113 unique_identifiers</strong>
sed -i 's/>//g' unique_fasta_headers
awk 'NR==FNR{array[$1];next}!($1 in array){print $1}' unique_fasta_headers unique_identifiers
<strong>NO_OUTPUT</strong>
awk 'NR==FNR{array[$1];next}!($1 in array){print $1}' unique_identifiers unique_fasta_headers
<strong>NO_OUTPUT</strong></pre>

Great. Our `gff3` and `fasta` match.

The sampled sequences were also provided by Julianna. The paired-end sequences are located at `/UCHC/PublicShare/jules/paired_end_fastas/` and the single-end sequences are located at `/UCHC/PublicShare/jules/single_end_fastas`.

<h2 id="Third_Point_eader">Quality control using sickle</h2>
<h2 id="combining">Concatenating mulitple runs per sample correctly</h2>
Because there are multiple runs per sample, the paired-end and single-end samples had to be combined appropriately. The samples were combined with the following code:
 <pre style="color: silver; background: black;">
cd paired_end_fastas
array=( $(ls .) )
i=0
while [ $i -lt $((${#array[@]}-6)) ];
do export BASENAME=$(basename $array[$i]});
echo ${array[$i]} ${array[$i+2]} ${array[$i+4]} ${array[$i+6]};
cat ${array[$i]} ${array[$i+2]} ${array[$i+4]} ${array[$i+6]} >> ../combined_paired_end_fastas/$BASENAME;
i=$(($i+8));
done;

<strong>I10-RPE1_S3_L001_R1_001.fastq.gz   I10-RPE1_S3_L002_R1_001.fastq.gz   I10-RPE1_S3_L003_R1_001.fastq.gz   I10-RPE1_S3_L004_R1_001.fastq.gz
I11-RPE2_S8_L001_R1_001.fastq.gz   I11-RPE2_S8_L002_R1_001.fastq.gz   I11-RPE2_S8_L003_R1_001.fastq.gz   I11-RPE2_S8_L004_R1_001.fastq.gz
I12-RPE3_S11_L001_R1_001.fastq.gz  I12-RPE3_S11_L002_R1_001.fastq.gz  I12-RPE3_S11_L003_R1_001.fastq.gz  I12-RPE3_S11_L004_R1_001.fastq.gz
I1-RE-8_S12_L001_R1_001.fastq.gz   I1-RE-8_S12_L002_R1_001.fastq.gz   I1-RE-8_S12_L003_R1_001.fastq.gz   I1-RE-8_S12_L004_R1_001.fastq.gz
I2-RE-10_S7_L001_R1_001.fastq.gz   I2-RE-10_S7_L002_R1_001.fastq.gz   I2-RE-10_S7_L003_R1_001.fastq.gz   I2-RE-10_S7_L004_R1_001.fastq.gz
I3-RE-12_S9_L001_R1_001.fastq.gz   I3-RE-12_S9_L002_R1_001.fastq.gz   I3-RE-12_S9_L003_R1_001.fastq.gz   I3-RE-12_S9_L004_R1_001.fastq.gz
I4-shA-5_S2_L001_R1_001.fastq.gz   I4-shA-5_S2_L002_R1_001.fastq.gz   I4-shA-5_S2_L003_R1_001.fastq.gz   I4-shA-5_S2_L004_R1_001.fastq.gz
I5-shD-2_S10_L001_R1_001.fastq.gz  I5-shD-2_S10_L002_R1_001.fastq.gz  I5-shD-2_S10_L003_R1_001.fastq.gz  I5-shD-2_S10_L004_R1_001.fastq.gz
I6-shE-3_S6_L001_R1_001.fastq.gz   I6-shE-3_S6_L002_R1_001.fastq.gz   I6-shE-3_S6_L003_R1_001.fastq.gz   I6-shE-3_S6_L004_R1_001.fastq.gz
I7-miA-1_S5_L001_R1_001.fastq.gz   I7-miA-1_S5_L002_R1_001.fastq.gz   I7-miA-1_S5_L003_R1_001.fastq.gz   I7-miA-1_S5_L004_R1_001.fastq.gz
I8-miC-1_S1_L001_R1_001.fastq.gz   I8-miC-1_S1_L002_R1_001.fastq.gz   I8-miC-1_S1_L003_R1_001.fastq.gz   I8-miC-1_S1_L004_R1_001.fastq.gz
I9-miD-1_S4_L001_R1_001.fastq.gz   I9-miD-1_S4_L002_R1_001.fastq.gz   I9-miD-1_S4_L003_R1_001.fastq.gz   I9-miD-1_S4_L004_R1_001.fastq.gz

</strong>

array=( $(ls .) )
i=1
while [ $i -lt $((${#array[@]}-6)) ];
do export BASENAME=$(basename $array[$i]});
echo ${array[$i]} ${array[$i+2]} ${array[$i+4]} ${array[$i+6]};
cat ${array[$i]} ${array[$i+2]} ${array[$i+4]} ${array[$i+6]} >> ../combined_paired_end_fastas/$BASENAME;
i=$(($i+8));
done;
<strong>I10-RPE1_S3_L001_R2_001.fastq.gz   I10-RPE1_S3_L002_R2_001.fastq.gz   I10-RPE1_S3_L003_R2_001.fastq.gz   I10-RPE1_S3_L004_R2_001.fastq.gz
I11-RPE2_S8_L001_R2_001.fastq.gz   I11-RPE2_S8_L002_R2_001.fastq.gz   I11-RPE2_S8_L003_R2_001.fastq.gz   I11-RPE2_S8_L004_R2_001.fastq.gz
I12-RPE3_S11_L001_R2_001.fastq.gz  I12-RPE3_S11_L002_R2_001.fastq.gz  I12-RPE3_S11_L003_R2_001.fastq.gz  I12-RPE3_S11_L004_R2_001.fastq.gz
I1-RE-8_S12_L001_R2_001.fastq.gz   I1-RE-8_S12_L002_R2_001.fastq.gz   I1-RE-8_S12_L003_R2_001.fastq.gz   I1-RE-8_S12_L004_R2_001.fastq.gz
I2-RE-10_S7_L001_R2_001.fastq.gz   I2-RE-10_S7_L002_R2_001.fastq.gz   I2-RE-10_S7_L003_R2_001.fastq.gz   I2-RE-10_S7_L004_R2_001.fastq.gz
I3-RE-12_S9_L001_R2_001.fastq.gz   I3-RE-12_S9_L002_R2_001.fastq.gz   I3-RE-12_S9_L003_R2_001.fastq.gz   I3-RE-12_S9_L004_R2_001.fastq.gz
I4-shA-5_S2_L001_R2_001.fastq.gz   I4-shA-5_S2_L002_R2_001.fastq.gz   I4-shA-5_S2_L003_R2_001.fastq.gz   I4-shA-5_S2_L004_R2_001.fastq.gz
I5-shD-2_S10_L001_R2_001.fastq.gz  I5-shD-2_S10_L002_R2_001.fastq.gz  I5-shD-2_S10_L003_R2_001.fastq.gz  I5-shD-2_S10_L004_R2_001.fastq.gz
I6-shE-3_S6_L001_R2_001.fastq.gz   I6-shE-3_S6_L002_R2_001.fastq.gz   I6-shE-3_S6_L003_R2_001.fastq.gz   I6-shE-3_S6_L004_R2_001.fastq.gz
I7-miA-1_S5_L001_R2_001.fastq.gz   I7-miA-1_S5_L002_R2_001.fastq.gz   I7-miA-1_S5_L003_R2_001.fastq.gz   I7-miA-1_S5_L004_R2_001.fastq.gz
I8-miC-1_S1_L001_R2_001.fastq.gz   I8-miC-1_S1_L002_R2_001.fastq.gz   I8-miC-1_S1_L003_R2_001.fastq.gz   I8-miC-1_S1_L004_R2_001.fastq.gz
I9-miD-1_S4_L001_R2_001.fastq.gz   I9-miD-1_S4_L002_R2_001.fastq.gz   I9-miD-1_S4_L003_R2_001.fastq.gz   I9-miD-1_S4_L004_R2_001.fastq.gz

</strong></pre>

We see that thesee files have been processed correctly. Now for the single end fastas:
<pre style="color: silver; background: black;">cd single_end_fastas
array=( $(ls .) )
i=0
while [ $i -lt $((${#array[@]}-4)) ];
do export BASENAME=$(basename $array[$i]});
echo ${array[$i]} ${array[$i+1]} ${array[$i+2]} ${array[$i+3]};
cat ${array[$i]} ${array[$i+1]} ${array[$i+2]} ${array[$i+3]} >> ../combined_single_end_fastas/$BASENAME;
i=$(($i+4));
done;
<strong>DaudiAgo1KD_S2_L001_R1_001.fastq.gz           DaudiAgo1KD_S2_L002_R1_001.fastq.gz           DaudiAgo1KD_S2_L003_R1_001.fastq.gz           DaudiAgo1KD_S2_L004_R1_001.fastq.gz
DaudiAgo2KD_S8_L001_R1_001.fastq.gz           DaudiAgo2KD_S8_L002_R1_001.fastq.gz           DaudiAgo2KD_S8_L003_R1_001.fastq.gz           DaudiAgo2KD_S8_L004_R1_001.fastq.gz
DaudiAgo3KD_S1_L001_R1_001.fastq.gz           DaudiAgo3KD_S1_L002_R1_001.fastq.gz           DaudiAgo3KD_S1_L003_R1_001.fastq.gz           DaudiAgo3KD_S1_L004_R1_001.fastq.gz
DaudiAgo4KD_S7_L001_R1_001.fastq.gz           DaudiAgo4KD_S7_L002_R1_001.fastq.gz           DaudiAgo4KD_S7_L003_R1_001.fastq.gz           DaudiAgo4KD_S7_L004_R1_001.fastq.gz
DaudiDicerKD_S4_L001_R1_001.fastq.gz          DaudiDicerKD_S4_L002_R1_001.fastq.gz          DaudiDicerKD_S4_L003_R1_001.fastq.gz          DaudiDicerKD_S4_L004_R1_001.fastq.gz
DaudiDroshaKD_S5_L001_R1_001.fastq.gz         DaudiDroshaKD_S5_L002_R1_001.fastq.gz         DaudiDroshaKD_S5_L003_R1_001.fastq.gz         DaudiDroshaKD_S5_L004_R1_001.fastq.gz
DaudiLaKD_S6_L001_R1_001.fastq.gz             DaudiLaKD_S6_L002_R1_001.fastq.gz             DaudiLaKD_S6_L003_R1_001.fastq.gz             DaudiLaKD_S6_L004_R1_001.fastq.gz
DaudiNoTxt_S3_L001_R1_001.fastq.gz            DaudiNoTxt_S3_L002_R1_001.fastq.gz            DaudiNoTxt_S3_L003_R1_001.fastq.gz            DaudiNoTxt_S3_L004_R1_001.fastq.gz
I26-Hsa-DicerKD_S10_L001_R1_001.fastq.gz      I26-Hsa-DicerKD_S10_L002_R1_001.fastq.gz      I26-Hsa-DicerKD_S10_L003_R1_001.fastq.gz	  I26-Hsa-DicerKD_S10_L004_R1_001.fastq.gz
I27-Hsa-DroshaKD_S9_L001_R1_001.fastq.gz      I27-Hsa-DroshaKD_S9_L002_R1_001.fastq.gz      I27-Hsa-DroshaKD_S9_L003_R1_001.fastq.gz	  I27-Hsa-DroshaKD_S9_L004_R1_001.fastq.gz
I28-Hsa-Ago1KD_S6_L001_R1_001.fastq.gz        I28-Hsa-Ago1KD_S6_L002_R1_001.fastq.gz        I28-Hsa-Ago1KD_S6_L003_R1_001.fastq.gz        I28-Hsa-Ago1KD_S6_L004_R1_001.fastq.gz
I29-Hsa-Ago2KD_S8_L001_R1_001.fastq.gz        I29-Hsa-Ago2KD_S8_L002_R1_001.fastq.gz        I29-Hsa-Ago2KD_S8_L003_R1_001.fastq.gz        I29-Hsa-Ago2KD_S8_L004_R1_001.fastq.gz
I30-Hsa-Ago3KD_S5_L001_R1_001.fastq.gz        I30-Hsa-Ago3KD_S5_L002_R1_001.fastq.gz        I30-Hsa-Ago3KD_S5_L003_R1_001.fastq.gz        I30-Hsa-Ago3KD_S5_L004_R1_001.fastq.gz
I31-Hsa-Ago4KD_S3_L001_R1_001.fastq.gz        I31-Hsa-Ago4KD_S3_L002_R1_001.fastq.gz        I31-Hsa-Ago4KD_S3_L003_R1_001.fastq.gz        I31-Hsa-Ago4KD_S3_L004_R1_001.fastq.gz
I32-Hsa-LaSSB-KD_S7_L001_R1_001.fastq.gz      I32-Hsa-LaSSB-KD_S7_L002_R1_001.fastq.gz      I32-Hsa-LaSSB-KD_S7_L003_R1_001.fastq.gz	  I32-Hsa-LaSSB-KD_S7_L004_R1_001.fastq.gz
RE-8-TruSeq_Ago1KD_S4_L001_R1_001.fastq.gz    RE-8-TruSeq_Ago1KD_S4_L002_R1_001.fastq.gz    RE-8-TruSeq_Ago1KD_S4_L003_R1_001.fastq.gz    RE-8-TruSeq_Ago1KD_S4_L004_R1_001.fastq.gz
RE-8-TruSeq_Ago2KD_S5_L001_R1_001.fastq.gz    RE-8-TruSeq_Ago2KD_S5_L002_R1_001.fastq.gz    RE-8-TruSeq_Ago2KD_S5_L003_R1_001.fastq.gz    RE-8-TruSeq_Ago2KD_S5_L004_R1_001.fastq.gz
RE-8-TruSeq_Ago3KD_S8_L001_R1_001.fastq.gz    RE-8-TruSeq_Ago3KD_S8_L002_R1_001.fastq.gz    RE-8-TruSeq_Ago3KD_S8_L003_R1_001.fastq.gz    RE-8-TruSeq_Ago3KD_S8_L004_R1_001.fastq.gz
RE-8-TruSeq_Ago4KD_S7_L001_R1_001.fastq.gz    RE-8-TruSeq_Ago4KD_S7_L002_R1_001.fastq.gz    RE-8-TruSeq_Ago4KD_S7_L003_R1_001.fastq.gz    RE-8-TruSeq_Ago4KD_S7_L004_R1_001.fastq.gz
RE-8-TruSeq_DicerKD_S6_L001_R1_001.fastq.gz   RE-8-TruSeq_DicerKD_S6_L002_R1_001.fastq.gz   RE-8-TruSeq_DicerKD_S6_L003_R1_001.fastq.gz   RE-8-TruSeq_DicerKD_S6_L004_R1_001.fastq.gz
RE-8-TruSeq_DroshaKD_S1_L001_R1_001.fastq.gz  RE-8-TruSeq_DroshaKD_S1_L002_R1_001.fastq.gz  RE-8-TruSeq_DroshaKD_S1_L004_R1_001.fastq.gz  RE-8-TruSeq_DroskaKD_S1_L003_R1_001.fastq.gz
RE-8-TruSeq_LaKD_S3_L001_R1_001.fastq.gz      RE-8-TruSeq_LaKD_S3_L002_R1_001.fastq.gz      RE-8-TruSeq_LaKD_S3_L003_R1_001.fastq.gz	  RE-8-TruSeq_LaKD_S3_L004_R1_001.fastq.gz
SNU-ago1KD_S5_L001_R1_001.fastq.gz            SNU-ago1KD_S5_L002_R1_001.fastq.gz            SNU-ago1KD_S5_L003_R1_001.fastq.gz            SNU-ago1KD_S5_L004_R1_001.fastq.gz
SNU-ago2KD_S6_L001_R1_001.fastq.gz            SNU-ago2KD_S6_L002_R1_001.fastq.gz            SNU-ago2KD_S6_L003_R1_001.fastq.gz            SNU-ago2KD_S6_L004_R1_001.fastq.gz
SNU-ago3KD_S7_L001_R1_001.fastq.gz            SNU-ago3KD_S7_L002_R1_001.fastq.gz            SNU-ago3KD_S7_L003_R1_001.fastq.gz            SNU-ago3KD_S7_L004_R1_001.fastq.gz
SNU-ago4KD_S4_L001_R1_001.fastq.gz            SNU-ago4KD_S4_L002_R1_001.fastq.gz            SNU-ago4KD_S4_L003_R1_001.fastq.gz            SNU-ago4KD_S4_L004_R1_001.fastq.gz
SNU-dicerKD_S8_L001_R1_001.fastq.gz           SNU-dicerKD_S8_L002_R1_001.fastq.gz           SNU-dicerKD_S8_L003_R1_001.fastq.gz           SNU-dicerKD_S8_L004_R1_001.fastq.gz
SNU-droshaKD_S3_L001_R1_001.fastq.gz          SNU-droshaKD_S3_L002_R1_001.fastq.gz          SNU-droshaKD_S3_L003_R1_001.fastq.gz          SNU-droshaKD_S3_L004_R1_001.fastq.gz

</strong></pre>

We see the fastas were combined appropriately. We are now ready to trim our data.
<h2 id="trimming">Trimming reads with sickle</h2>


The paired-end reads were trimmed with the following code:
<pre style="color: silver; background: black;">
module load sickle

cd combined_paired_end_fastas
array=( $(ls .) )
i=0
while [ $i -lt ${#array[@]} ]; 
do export BASENAME=$(basename ${array[$i]} _L001_R1_001.fastq.gz).trimmed; 
sickle pe \
-t sanger \
-f ${array[$i]} -r ${array[$i+1]} \
-o ../trimmed_combined_paired_end_fastas/$BASENAME.R1.fastq \
-p ../trimmed_combined_paired_end_fastas/$BASENAME.R2.fastq \
-s ../trimmed_combined_paired_end_fastas/$BASENAME.singles.fastq \
-q 30 \
-l 50; 
i=$(( $i + 2 )); 
done;
</pre>

Should the wrong pair be processed `sickle` will return an error. After checking the standard output it was verified that all pairs were properly trimmed.

The single-end fastas were trimmed with the following code:
<pre style="color: silver; background: black;">
module load sickle
cd combined_single_end_fastas

array=( $(ls .) )
i=0
while [ $i -lt ${#array[@]} ]; 
do export BASENAME=$(basename ${array[$i]} .fastq.gz).trimmed; 
sickle se \
-f ${array[$i]} \
-t sanger \
-o ../trimmed_single_end_fastas/$BASENAME.fastq.gz \
-q 30 \
-l 50; 
i=$(( $i + 1 )); 
done;
</pre>

The trimmed files are in `/UCHC/PublicShare/jules/trimmed_combined/paired_end_fastas/` and `/UCHC/PublicShare/jules/trimmed_single_end_fastas/'.

<h2 id="Fourth_Point_Header">Aligning reads using HISAT2</h2>

<h2 id="index">Building the index</h2>

The index was built with the following code:

