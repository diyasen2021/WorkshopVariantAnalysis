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

---

### 1.2 Common NGS Platforms

| Platform | Technology | Read Length | Typical Use |
|--------|------------|-------------|-------------|
| Illumina | Sequencing by synthesis | 50–300 bp | Clinical & research |
| PacBio | SMRT sequencing | 10–25 kb | Long-read genomics |
| Oxford Nanopore | Nanopore sensing | Up to Mb | Real-time, field sequencing |

Illumina platforms dominate clinical genomics due to their low error rates and extensive validation.

**Reference:**  
https://www.illumina.com/science/technology/next-generation-sequencing.html

https://www.illumina.com/content/dam/illumina-marketing/documents/products/illumina_sequencing_introduction.pdf

https://microbenotes.com/illumina-sequencing/

---

## 2. DNA-seq Workflow

DNA sequencing is used to identify genomic variants such as SNPs, insertions/deletions (indels), copy number variations (CNVs), and structural variants.

### 2.1 DNA-seq Workflow Overview

1. **Sample collection**
   Collect tissue, blood, cells, or environmental samples
   Preserve sample integrity (fresh, frozen, RNAlater)
   Avoid contamination and degradation
   
3. **DNA extraction**
   Extract DNA or RNA
   Assess concentration (e.g., Qubit/Nanodrop)
   Assess integrity (e.g., gel electrophoresis, Bioanalyzer)
   
4. **Library preparation**
   Fragmentation of DNA by mechanical or enzymatic methods
   Adapter ligation (to attach sequencing primers or barcodes)
   PCR amplification
   
5. **Sequencing**
   Load libraries onto sequencing platforms
   Common platform Illumina
   Generate millions of reads per sample
   
6. **Base calling & demultiplexing**
   Convert raw instrument signals into nucleotide sequences
   Output fastq files
   
7. **Bioinformatics analysis**
    Raw data QC
    Read preprocessing
    Mapping reads to reference
    Variant calling
    Variant annotation
    Interpretation and reporting
    Data storage and reproducibility

---

### 2.2 Types of DNA-seq Experiments

DNA sequencing experiments can be broadly classified based on how much of the genome is sequenced. The three most commonly used approaches are:
   - Whole Genome Sequencing (WGS)
   - Whole Exome Sequencing (WES)
   - Targeted gene panels
     
Each approach differs in cost, genomic coverage, data complexity, and clinical applicability.

1. **Whole Genome Sequencing (WGS)**

   **What is WGS?**
   Whole Genome Sequencing (WGS) sequences the entire genome, including:
   Protein-coding regions (exons)
   Non-coding regions (introns, intergenic regions)
   Regulatory elements (promoters, enhancers)
   Mitochondrial DNA
   In humans, this corresponds to ~3.2 billion base pairs.

   **Allows detection of**
   SNPs and indels
   Copy number variations (CNVs)
   Structural variants (SVs)

   **Advantages of WGS**
   Unbiased genome-wide coverage
   Best method for detecting structural variants
   Suitable for rare disease discovery

   **Limitations**
   Highest cost among DNA-seq methods
   Large data storage and computational requirements
   More complex variant interpretation
   Many variants of unknown significance (VUS)

📌 WGS is powerful but not always necessary for routine diagnostics.

2. **Whole Exome Sequencing (WES)**

   **What is WES?**
   Whole Exome Sequencing targets only the exonic (protein-coding) regions of the genome.
   Exons make up ~1–2% of the human genome
   ~85% of known disease-causing variants lie in exons
   DNA is fragmented
   Exonic regions are enriched using capture probes
   Only captured regions are sequenced

   **Allows detection of**
   What questions does WES answer?
   SNPs and indels
   Variants affecting protein function

   **Advantages**
   Lower cost than WGS
   Reduced data size
   Higher coverage of coding regions
   Easier interpretation than WGS
   Mendelian disease mutations

   **Limitations**
   Misses non-coding variants
   Limited detection of structural variants
   Uneven coverage due to capture bias
   Cannot detect variants outside targeted exons

📌 WES offers a balance between coverage and cost.

3. **Targeted Gene Panels**

   **What are targeted gene panels?**
   Targeted panels sequence a predefined set of genes associated with a specific disease or phenotype.
   Examples include cancer panels, cardiac disease panels, neurological disorder panels
   Capture or amplify only selected genes
   Sequence at very high depth

   **Allows detection of**

   **Advantages**
   Lowest cost
   Very high coverage (deep sequencing)
   Fast turnaround time
   Simplified analysis and interpretation

   **Limitations**
   Limited to known genes
   Cannot discover novel disease genes
   Requires frequent panel updates

📌 Panels are ideal when the clinical question is well defined.
   

| Feature                | WGS                     | WES           | Targeted Panels    |
| ---------------------- | ----------------------- | ------------- | ------------------ |
| Genomic coverage       | Entire genome           | Exons only    | Selected genes     |
| % of genome sequenced  | ~100%                   | ~1–2%         | <1%                |
| Cost                   | High                    | Medium        | Low                |
| Data volume            | Very large              | Moderate      | Small              |
| Variant types detected | SNPs, indels, CNVs, SVs | SNPs, indels  | Mostly SNPs/indels |
| Clinical use           | Advanced diagnostics    | Rare diseases | Routine testing    |


   **Choosing the Right Approach**

   The choice of DNA-seq method depends on:
   Clinical or research question
   Budget and turnaround time
   Need for novel variant discovery
   Data analysis and storage capacity

📌 More sequencing is not always better — the best approach is the one that answers the question effectively.

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

FASTQ files are the primary output of next-generation sequencing (NGS) platforms. They store raw sequencing reads along with a measure of confidence for each base call, known as base quality scores. FASTQ files are typically the starting point for all downstream bioinformatics analyses.

**What Does a FASTQ File Contain?**
Each sequencing read in a FASTQ file is represented by four lines:

```
@READ001 Sample1
ACTGATCGATCGTTAGCTAGCTAGCTAGCTA
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF

@READ002 Sample1
TTCGATCGATCGATACGATCGATCGATCGA
+
FFFFFF:FFFFFFFFFFFFFFFFFFF:FFFF

@READ003 Sample1
GGGATCGATCGATCGGATATATCGCGTATA
+
FFFDDDAAADDDFFFFFDDDDFFFDDDAAA
```

 1. @READ001
    A unique identifier for the read. May include information about the instrument, flow cell, and lane

 2. SEQUENCE
    The nucleotide sequence (A, C, G, T, or N)
 
 3. +
    Acts as a separator between the sequence and quality scores

 4. QUALITY_SCORES
    Encodes the quality of each base in the sequence
    Each character corresponds to a base in the sequence line

📌 The length of the QUALITY_SCORES line is always equal to the length of the SEQUENCE line.

**Base Quality Scores and Phred Encoding**

Quality scores in FASTQ files are encoded using Phred scores, which represent the probability that a base call is incorrect.

   Q = Phred quality score
   P = probability that the base call is wrong

| Phred Score | Error Probability | Base Call Accuracy |
| ----------- | ----------------- | ------------------ |
| 10          | 1 in 10           | 90%                |
| 20          | 1 in 100          | 99%                |
| 30          | 1 in 1000         | 99.9%              |
| 40          | 1 in 10,000       | 99.99%             |

📌 Higher Phred scores indicate greater confidence in the base call.

In FASTQ files, Phred scores are stored as ASCII characters rather than numbers to reduce file size.
Most modern sequencing data uses Phred+33 encoding
Each character represents a numeric quality score + 33

Reference:
https://en.wikipedia.org/wiki/FASTQ_format

---

## 4.2 SAM and BAM

1. **SAM:** Sequence Alignment Map (text)
SAM is a human‑readable, tab‑delimited text format that stores alignment information for each sequencing read. Because it is text-based, it is easy to inspect manually and suitable for teaching and debugging.

**Key features of SAM file**
 - Stores one read per line
 - Contains header lines for
      - reference genome,
      - read groups,
      - alignment parameters
- Contains alignment records describing each read and how it maps

**Core Columns in SAM file (Mandatory Fields)**
   QNAME – Read name
   FLAG – Integer representing read properties (e.g., paired, mapped, reversed)
   RNAME – Reference chromosome read was aligned to
   POS – Starting position of alignment
   MAPQ – Mapping quality (confidence of alignment)
   CIGAR – How the read aligns (matches, insertions, deletions)
   RNEXT / PNEXT – Mate information (paired-end)
   TLEN – Template length (paired-end)
   SEQ – The read sequence
   QUAL – Base quality scores

Specification:  
https://samtools.github.io/hts-specs/SAMv1.pdf

2. **BAM (Binary Alignment Map — compressed binary format)**
BAM is the binary, compressed version of SAM. It contains the exact same alignment information but stored in a space-efficient binary format.

**Key features of BAM file**
- Smaller file size than SAM
- Required for visualization tools (e.g., IGV, JBrowse)
- Standard for real analysis pipelines due to speed and efficiency
- Can be indexed (.bai) for fast access


Differences between SAM and BAM files

| Feature      | SAM                 | BAM                      |
|--------------|----------------------|---------------------------|
| Format       | Text                 | Binary                    |
| Human-readable | Yes                | No                        |
| File size    | Large                | Small                     |
| Speed        | Slow to process      | Very fast                 |
| Indexing     | No                   | Yes (.bai)                |
| Used for     | Debugging, teaching  | Real analysis workflows   |

📌 SAM is for readability; BAM is for real analysis.

---

4.3. **VCF (Variant Call Format)**

VCF is a human‑readable, tab‑delimited text format used to store genetic variants identified from sequencing data. It combines both variant‑level information (e.g., chromosome, position, alleles) and sample‑level genotype data. 

**Key features of VCF file**
- Stores variant records, one per line
- Contains header lines (starting with ##) describing:
   - Reference genome
   - Annotation fields
   - Variant filters
   - FORMAT field definitions
- Contains a main header line (starting with #CHROM) listing column names
- Supports multi‑sample variant data in one file
- Can be indexed (.tbi, .csi) for rapid random access
- Used extensively in clinical genomics

**What are VCF files used for**

- Storing SNPs, indels, and structural variants
- Variant filtering
- Variant annotation (clinical + functional)
- Reporting variants in clinical genomics
- Multi‑sample comparison in population studies

Specification:  
https://samtools.github.io/hts-specs/VCFv4.3.pdf

---

4.4 **GTF and GFF Genome Annotation Files**

GTF is a human‑readable, tab‑delimited text format used to describe gene structures and genomic features such as genes, transcripts, exons, start codons, UTRs, etc.
It is widely used in RNA‑seq pipelines for read counting (e.g., with featureCounts or HTSeq) and understanding gene models.

**Key features of GTF file**
- Text-based, tab‑delimited format
- Describes genomic features and gene annotations
- Used to define:
   - Gene boundaries
   - Transcript structure
   - Exons, CDS, UTRs
- Contains 9 columns, with the last column (attributes) in a structured key–value format
- Compatible with most RNA‑seq workflows

Ensembl guide:  
https://useast.ensembl.org/info/website/upload/gff.html

GFF (versions 2 and 3) is a flexible, tab‑delimited annotation format that describes genomic features across any organism.
GFF3 is the modern standard and uses a structured, hierarchical system to represent genes, transcripts, and exons.

**Key features of GFF file**
- Similar to GTF but more general and more flexible
- Uses 9 columns like GTF
- Attributes follow a tag=value structure (GFF3 uses semicolon-separated key-value pairs)
- More widely used in non‑human genomes
- Many genome browsers and annotation tools prefer GFF3

Specification:
https://github.com/The-Sequence-Ontology/Specifications/blob/master/gff3.md

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

### 5.1 Reference Genomes and Annotations

What is a reference genome?
A reference genome is a curated, **representative** DNA sequence used as a coordinate system for aligning sequencing reads and reporting genomic features and variants. Its not built from a single individual, rather from multiple individuals.

In DNA-seq analysis, all downstream steps—alignment, variant calling, annotation, and interpretation—depend on the reference genome used.

Sequencing reads are mapped to this reference to identify differences such as:

- Single nucleotide variants (SNVs)

- Insertions and deletions (INDELs)

- Structural variants

- Copy number changes

**How is a reference genome created?**

- DNA is pooled from multiple individuals
- WGS sequencing is done
- Assembly of genome
- Consensus sequence is built

📌 Key idea: A variant is always defined relative to a reference genome.

| Reference         | Description                                      | Status                                  |
| ----------------- | ------------------------------------------------ | --------------------------------------- |
| **GRCh37 / hg19** | Older human reference genome                     | Still widely used in clinical pipelines |
| **GRCh38 / hg38** | Updated human reference with improved assemblies | Current recommended standard            |
| **T2T-CHM13**     | Telomere-to-telomere complete genome             | Research use, emerging adoption         |


🔗 Genome Reference Consortium:
https://www.ncbi.nlm.nih.gov/grc

🔗 UCSC Genome Browser assemblies:
https://genome.ucsc.edu/cgi-bin/hgGateway

**Why Reference Choice Matters**

- Read alignment accuracy

- Variant coordinates

- Gene annotations

**Clinical interpretation**

Clinical variant example: EGFR L858R (lung cancer)

EGFR L858R is a well-known activating somatic mutation in non-small cell lung cancer (NSCLC).
It predicts sensitivity to EGFR tyrosine kinase inhibitors (e.g. erlotinib, gefitinib, osimertinib).

The same variant in two genome builds
Variant description (protein level)

Gene: EGFR

Transcript: NM_005228.5

cDNA: c.2573T>G

Protein: p.Leu858Arg (L858R)

| Genome build      | Coordinate      | Variant |
| ----------------- | --------------- | ------- |
| **GRCh37 (hg19)** | chr7:55,259,515 | T > G   |
| **GRCh38 (hg38)** | chr7:55,086,724 | T > G   |


This biological change is identical, regardless of genome build.
Clinical databases (ClinVar, gnomAD) are reference-specific


Mixing references leads to annotation errors ❌

⚠️ Always ensure alignment, variant calling, annotation databases, and clinical interpretation use the same reference build.

**What Are Genome Annotations?**

While the reference genome provides the sequence, annotations describe:

- Where genes are located

- How genes are structured

- Which regions are functional


Annotations are essential for answering questions like:

Is this variant in a gene?

Does it affect protein coding?

Is it in a regulatory region?

Types of Genomic Annotations
1. Gene Models

Define the genomic locations of genes:

Gene start and end

Strand orientation (+ / −)


Common sources:

🔗 GENCODE: https://www.gencodegenes.org

🔗 RefSeq: https://www.ncbi.nlm.nih.gov/refseq

🔗 Ensembl: https://www.ensembl.org

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
