# Module 1: NGS & Clinical Genomics Basics  
**Duration:** 3 hours  
**Target audience:** Early-career researchers, graduate students, clinicians transitioning into genomics  

---

## Learning Objectives

By the end of this module, learners will be able to:

- Understand the principles of next-generation sequencing (NGS)
- Distinguish between DNA-seq and RNA-seq workflows
- Differentiate clinical genomics pipelines from research pipelines
- Identify and interpret common genomics file formats
- Understand the role of reference genomes and annotations
- Perform basic exploration of real sequencing data files

---

## 1. Introduction to Next-Generation Sequencing (NGS)

### 1.1 What is NGS?

Next-generation sequencing (NGS) refers to high-throughput sequencing technologies that enable simultaneous sequencing of millions to billions of DNA or RNA fragments. This parallelization dramatically reduces cost and time compared to traditional Sanger sequencing.

Key characteristics of NGS:

- **Massively parallel sequencing**
- **High throughput**
- **Short to long read lengths depending on platform**
- **Requires computational analysis**

NGS has revolutionized genomics by enabling applications such as whole-genome sequencing (WGS), whole-exome sequencing (WES), transcriptomics, metagenomics, and single-cell analysis.

**Reference:**  
https://www.illumina.com/science/technology/next-generation-sequencing.html

https://www.illumina.com/content/dam/illumina-marketing/documents/products/illumina_sequencing_introduction.pdf

https://microbenotes.com/illumina-sequencing/

---

### 1.2 Common NGS Platforms

| Platform | Technology | Read Length | Typical Use |
|--------|------------|-------------|-------------|
| Illumina | Sequencing by synthesis | 50–300 bp | Clinical & research |
| PacBio | SMRT sequencing | 10–25 kb | Long-read genomics |
| Oxford Nanopore | Nanopore sensing | Up to Mb | Real-time, field sequencing |

Illumina platforms dominate clinical genomics due to their low error rates and extensive validation.

---

## 2. DNA-seq Workflow

DNA sequencing is used to identify genomic variants such as SNPs, insertions/deletions (indels), copy number variations (CNVs), and structural variants.

### 2.1 DNA-seq Workflow Overview

1. **Sample collection**
2. **DNA extraction**
3. **Library preparation**
   - Fragmentation
   - Adapter ligation
4. **Sequencing**
5. **Base calling & demultiplexing**
6. **Bioinformatics analysis**

**Typical analysis flow:**


---

### 2.2 Types of DNA-seq Experiments

- Whole Genome Sequencing (WGS)
- Whole Exome Sequencing (WES)
- Targeted gene panels

Each differs in cost, coverage, and clinical applicability.

**Reference:**  
https://www.nature.com/articles/nrg2626

---


## 3. Clinical vs Research Genomics Pipelines

### 3.1 Clinical Genomics Pipelines

Clinical pipelines are designed for diagnostic or therapeutic decision-making and must meet regulatory standards.

Key features:

- Validated tools and workflows
- Quality control at every step
- Traceability and audit trails
- Standardized reporting
- Regulatory compliance (CLIA, CAP, ISO)

Clinical outputs must be interpretable, reproducible, and clinically actionable.

---

### 3.2 Research Pipelines

Research pipelines prioritize flexibility and discovery.

Characteristics:

- Frequent tool updates
- Exploratory analyses
- Custom parameters
- Hypothesis-driven

**Important:** Research pipelines are not suitable for clinical decision-making without validation.

---

## 4. Common File Formats in Genomics

Understanding genomics file formats is essential for both analysis and interpretation.

---

## 4.1 FASTQ

FASTQ files store raw sequencing reads along with base quality scores.

Structure:

@SEQ_ID
SEQUENCE
+
QUALITY_SCORES


Quality scores are encoded using Phred scores.

Tool for QC:  
FastQC – https://www.bioinformatics.babraham.ac.uk/projects/fastqc/

---

## 4.2 SAM and BAM

- **SAM:** Sequence Alignment Map (text)
- **BAM:** Binary version of SAM (compressed)

Contains:
- Alignment position
- Mapping quality
- Read flags
- Optional tags

Specification:  
https://samtools.github.io/hts-specs/SAMv1.pdf

---

## 4.3 VCF (Variant Call Format)

VCF files store genomic variants and genotypes.

Example:

chr1 12345 . A G 99 PASS DP=100 GT 0/1


Used extensively in clinical genomics.

Specification:  
https://samtools.github.io/hts-specs/VCFv4.3.pdf

---

## 4.4 GTF and GFF

Annotation formats describing genes, transcripts, and genomic features.

Used in RNA-seq and variant annotation.

Ensembl guide:  
https://useast.ensembl.org/info/website/upload/gff.html

---

## 4.5 BED

BED files define genomic intervals.

Common use cases:
- Regions of interest
- Peaks
- Target capture regions

Specification:  
https://genome.ucsc.edu/FAQ/FAQformat.html

---

## 5. Reference Genomes and Annotations

### 5.1 Reference Genomes

A reference genome is a representative DNA sequence used for read alignment and variant calling.

Common human references:
- GRCh38 (current standard)
- GRCh37 (legacy)

Sources:
- NCBI Genome – https://www.ncbi.nlm.nih.gov/genome
- Ensembl – https://www.ensembl.org

---

### 5.2 Annotations

Annotations provide biological meaning to genomic coordinates.

Include:
- Gene models
- Transcripts
- Functional elements

Tools like UCSC Genome Browser allow interactive exploration:
https://genome.ucsc.edu

---

## 6. Hands-On: Exploring Real Sequencing Files

### 6.1 Required Tools

- FastQC  
- Samtools  
- IGV (optional visualization)

---

### 6.2 Exercise 1: FASTQ Quality Control

Download a FASTQ file from ENCODE:  
https://www.encodeproject.org

Run:

```bash
fastqc sample.fastq.gz
```

Questions:

Are there quality drops at read ends?

Is adapter contamination present?


### 6.3 Exercise 2: BAM File Exploration

```samtools view -h sample.bam | head
samtools idxstats sample.bam
```
Interpret:

Mapping rates

Chromosomal distribution

### Exercise 6.4: VCF File Inspection

Download a VCF from 1000 Genomes:
https://www.internationalgenome.org/data

### Key takeaways:

NGS enables high-throughput genomic analysis

Clinical genomics requires validated, regulated pipelines

File formats form the backbone of genomics workflows

Reference genomes and annotations are critical for interpretation
