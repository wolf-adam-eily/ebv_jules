# ebv_jules

<div id="toc_container">
<p class="toc_title">Contents</p>
<ul class="toc_list">
  <li><a href="#First_Point_Header">1 Overview and directory layout</a></li>
<li><a href="#Second_Point_Header">2 Gathering materials and directory layout</a></li>
	<ol><li><a href="#sickle">Trimming using sickle</a></li>
		<li><a href="#fastqc">Quality control statistics using fastqc and multiqc</a></li></ol>
</ul>
 
 <h2 id="First_Point_Header>Overview and directory layout</h2>
  
  Epidemiologists have found that different populations of the world manifest different long-term phenotypes from infection from the Epstein-Barr virus. Most notably that infected Westerners most often develop mononucleosis while Easterners, particularly Eastern Asians often go on to develop various forms of cancers. The hypothesis of this experiment is that various SNPs differences between Easterners and Westerners influence cellular populations of various interfering RNAs, affecting phenotype. Furthermore, Julianna Crivello, the lead investigator of this research proposes that Western SNPs cause greater production of various interfering RNAs which limit the extent of disease while Eastern SNPs cause lower production of various interfering RNAs, increasing chance of more serious disease, i.e., cancers. A variety of cell lines were cultured for this experiment, they are:
 
 
 TOTAL RNA LIBRARIES:
 (RPE stands for human retinal pigmented epithelial cell lines)
 <<pre style="color: silver; background: black;">>
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
