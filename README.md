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
 
 To begin, the EBV, small RNAs, and Spike-in fastas and gtfs were provided by Julianna. They reside at: `/UCHC/PublicShare/jules/jules_stuff`. The sampled sequences were also provided by Julianna. The paired-end sequences are located at `/UCHC/PublicShare/jules/paired_end_fastas/` and the single-end sequences are located at `/UCHC/PublicShare/jules/single_end_fastas`.
