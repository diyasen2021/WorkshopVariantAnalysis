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


