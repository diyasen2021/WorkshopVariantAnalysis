# Module 2: Variant File Generation (DNA-seq)

**Estimated duration:** 7 hours
**Audience:** Graduate students / early professionals in genomics & bioinformatics
**Pedagogical style:** HCB – concept-heavy, biology-first, supported by hands-on reasoning

---

## Learning Objectives

At the end of this module, students should not only be able to *run* a variant calling workflow, but also **reason about why each step exists, what assumptions it makes, and how errors propagate** from raw reads into final variant calls. 
Specifically, students will be able to:

* Explain how sequencing quality and alignment artifacts influence variant calling accuracy
* Interpret alignment files (SAM/BAM) beyond surface-level statistics
* Describe how SNVs and INDELs arise biologically and how callers attempt to detect them
* Clearly distinguish germline and somatic variant calling from both biological and computational perspectives
* Read, interpret, and critically evaluate a VCF file
* Apply principled filtering to variant calls and justify filtering choices

---

## Module Structure (7 hours)

| Section | Topic                                                   | Time  |
| ------- | ------------------------------------------------------- | ----- |
| 2.1     | Quality Control Concepts                                | 1.0 h |
| 2.2     | Read Alignment Fundamentals                             | 1.5 h |
| 2.3     | Variant Calling: SNVs and INDELs                        | 2.0 h |
| 2.4     | Germline vs Somatic Variant Calling                     | 0.5 h |
| 2.5     | Understanding the VCF Format                            | 1.0 h |
| 2.6     | Hands-on: Alignment Review, Variant Calling & Filtering | 1.0 h |

---

## 2.1 Quality Control Concepts (1 hour)

### Why Quality Control Is Foundational

Variant calling is extremely sensitive to noise. Every sequencing experiment contains a mixture of true biological signal and technical artifacts introduced during DNA extraction, library preparation, amplification, and sequencing. Quality control (QC) is the process of identifying, quantifying, and contextualizing these artifacts *before* they are mistaken for biological variants.

A key conceptual point is that **QC is not about achieving perfect data**. Instead, QC helps us understand the *limitations* of a dataset so that downstream analyses can be interpreted appropriately. A variant caller does not know whether a base is unreliable unless we tell it so—either explicitly through filtering or implicitly through quality scores.

---

### Read-level Quality Metrics

At the raw read stage (FASTQ files), QC focuses on the properties of individual sequencing reads. Per-base Phred quality scores reflect the probability that a base has been called incorrectly. A systematic drop in quality toward the 3′ end of reads is common and reflects sequencing chemistry limitations rather than biological variation.

GC content distributions are another critical metric. Extreme GC bias can indicate amplification artifacts or capture inefficiencies and often results in uneven coverage across the genome. Adapter contamination, if not removed, can lead to misalignments and false variant calls near read ends.

---

### Alignment-level Quality Metrics

Once reads are aligned to a reference genome, QC shifts from individual bases to *patterns across reads*. Metrics such as percentage of mapped reads, duplication rates, and depth of coverage provide insight into library complexity and experimental success.

Coverage deserves special attention. Variant calling assumes sufficient read depth to distinguish true variants from sequencing errors. Low coverage regions increase false negatives, while excessively high coverage regions—often caused by PCR duplication or repeats—can inflate false positives.

---

### Conceptual Insight (HCB emphasis)

> QC metrics are proxies for biological and technical processes. Interpreting QC correctly requires asking *what went wrong upstream*, not merely whether a number passes a threshold.

---

## 2.2 Read Alignment Fundamentals (1.5 hours)

### What Does Alignment Actually Mean?

Read alignment is the process of determining the most likely genomic origin of each sequencing read. Because reads are short relative to the genome and may contain sequencing errors or true variants, alignment is inherently probabilistic rather than exact.

A crucial idea for students is that **alignment is a hypothesis**, not a fact. Each read is placed where the aligner believes it fits best, given the reference genome and an error model. Downstream analyses—including variant calling—depend heavily on the correctness of these hypotheses.

---

### Reference Genomes and Their Consequences

The reference genome is not a universal truth but a representative assembly. Differences between GRCh37 and GRCh38, alternate contigs, and unplaced scaffolds all influence where reads align. Misalignment due to reference choice can directly create false variants or mask real ones.

Consistency is essential: the same reference build must be used for alignment, variant calling, and annotation.

---

### How Aligners Work (Conceptual Overview)

Modern short-read aligners typically use a seed-and-extend strategy. Short exact or near-exact matches (seeds) are first identified, followed by extension allowing mismatches and gaps. Under the hood, data structures such as the Burrows–Wheeler Transform enable fast searching across the genome.

Importantly, aligners must trade off speed against sensitivity. Highly repetitive regions, paralogous genes, and low-complexity sequences remain challenging even for state-of-the-art tools.

---

### SAM/BAM Files as Evidence

SAM and BAM files are not merely intermediate formats; they are *evidence records*. Each aligned read carries information about where it mapped, how confidently it mapped (MAPQ), and how well it matched the reference (CIGAR string).

Students should learn to read these fields as a narrative: does the alignment make biological sense, or does it show signs of ambiguity?

---

## 2.3 Variant Calling: SNVs and INDELs (2 hours)

### What Is a Variant in Practice?

A variant represents a position where the sequenced sample differs from the reference genome. However, from a computational perspective, a variant is an **inference** made from a pileup of reads, each with its own error probabilities and alignment uncertainties.

Variant callers attempt to separate true biological variation from noise by integrating multiple signals simultaneously.

---

### SNVs: Detection and Interpretation

Single nucleotide variants (SNVs) are the most straightforward variant type to detect. When multiple high-quality reads consistently show the same alternate base at a genomic position, confidence in an SNV increases.

Nevertheless, systematic sequencing errors (e.g. context-specific errors) can mimic SNVs. Variant callers therefore consider base quality, strand bias, and local sequence context when assigning confidence scores.

---

### INDELs: Why They Are Harder

Insertions and deletions disrupt the alignment itself. Reads spanning an INDEL may align with mismatches or soft clipping unless the aligner and caller correctly model gaps. As a result, INDEL calling has higher false-positive and false-negative rates than SNV calling.

Modern callers often perform local realignment or haplotype reconstruction to improve INDEL detection.

---

### Variant Callers as Probabilistic Models

Variant callers do not make binary decisions. Instead, they compute likelihoods for different genotypes given the observed reads. The QUAL score in a VCF reflects confidence in the *existence* of a variant, not its biological importance.

Understanding this probabilistic foundation is critical for responsible interpretation.

---

## 2.4 Germline vs Somatic Variant Calling (0.5 hours)

### Biological Context

Germline variants are inherited and present in every cell, whereas somatic variants arise during an organism’s lifetime and may be present only in a subset of cells. This biological distinction fundamentally shapes computational strategy.

---

### Computational Implications

Germline variant calling assumes diploidy and relatively stable allele fractions. Somatic variant calling must detect low-frequency alleles against a noisy background and typically relies on tumor–normal comparisons to suppress technical artifacts.

Because of these challenges, somatic callers use stricter statistical models and more aggressive filtering.

---

## 2.5 Understanding the VCF Structure (1 hour)

### VCF as a Data Model

The Variant Call Format (VCF) is both human-readable and machine-parseable. It encodes not only variant positions, but also confidence measures, annotations, and sample-specific genotypes.

Students should view a VCF as a **rich hypothesis table**, not a definitive list of true variants.

---

### Core Fields Explained

Each row in a VCF describes a genomic locus, the reference allele, one or more alternate alleles, and a collection of annotations summarizing evidence across reads.

The INFO and FORMAT fields often contain the most critical information for filtering and interpretation.

---

## 2.6 Hands-on: Alignment Review, Variant Calling & Filtering (1 hour)

### Alignment Review

Students visually inspect BAM files using a genome browser to connect abstract metrics with concrete read patterns. Particular attention is paid to regions with apparent mismatches, low coverage, or conflicting evidence.

---

### Variant Calling Workflow

Using a pre-aligned BAM file, students generate a raw VCF and observe how many candidate variants are initially reported. This emphasizes that raw variant sets are intentionally permissive.

---

### VCF Parsing and Quality Filtering

Students apply depth, quality, and allele balance filters and observe how the variant set changes. Discussion focuses on trade-offs between sensitivity and specificity, reinforcing that filtering is a scientific judgment, not a mechanical step.

---

## Summary & Key Takeaways

Variant calling is an inferential process built on imperfect data. Understanding how QC, alignment, and statistical modeling interact is essential for producing reliable and interpretable variant datasets.

---

## Bridge to Next Module

In the next module, we move from *calling* variants to *understanding* them through annotation, functional interpretation, and clinical or biological context.
