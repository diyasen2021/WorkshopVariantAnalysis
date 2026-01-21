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

Choice of alignment tool is often determined by the type of NGS application being conducted. We have previously used STAR for RNA-seq data because it is fast and optimized for aligning spliced reads. For ChIP-Seq we used Bowtie2 to align the reads because it is fast and accurate. For variant calling we will use [BWA (Burrows-Wheeler Aligner)](http://bio-bwa.sourceforge.net) for alignment. 

BWA is generally slower than Bowtie2 with similar sensitivity and both tools can perform gapped alignment for the identification of indels and can effectively map paired-end reads. However, BWA is a bit more accurate and provides information on which alignments are trustworthy. Small numbers of bad alignments can result in many false variant calls, so accuracy is paramount, and is the basis for choosing BWA.

### BWA modes

Depending on read length, BWA has different modes optimized for different sequence lengths:

- **BWA-backtrack:** designed for Illumina sequence reads up to 100bp (3-step)

- **BWA-SW:** designed for longer sequences ranging from 70bp to 1Mbp, long-read support and split alignment

- **BWA-MEM:** shares similar features to BWA-SW, but BWA-MEM is the latest, and is generally recommended for high-quality queries as it is faster and more accurate. BWA-MEM also has better performance than BWA-backtrack for 70-100bp Illumina reads.

### Aligning reads with BWA-MEM

Change directories into the `reference_data` directory:

```bash
$ cd ~/var-calling/reference_data
```

#### Creating BWA-MEM index

Similar to the other alignment tools we have used, the first step in the BWA alignment is to create an index for the reference genome. Similar to Bowtie2, BWA indexes the genome with an FM Index based on the Burrows-Wheeler Transform to keep memory requirements low for the alignment process. 

The basic options for indexing the genome using BWA are:

* `-p`: prefix for all index files

```bash
$ module load gcc/6.2.0 bwa/0.7.8

$ bwa index -p chr20 chr20.fa
```

#### Aligning reads with BWA-MEM

Now that we have our indexes created, we can get started with read alignment. Change directories to the `var-calling` folder:

```bash
$ cd ../
```

We will perform alignment on our paired-end reads for sample `na12878`. Details on BWA and its functionality can be found in the [user manual](http://bio-bwa.sourceforge.net/bwa.shtml); we encourage you to peruse through to get familiar with all available options.

The basic options for aligning reads to the genome using BWA-MEM are:

* `-t`: number of threads / cores
* `-M`: mark shorter split hits as secondary
	> This is optional for Picard compatibility as MarkDuplicates can directly process BWA's alignment, whether or not the alignment marks secondary hits. However, if we want MergeBamAlignment to reassign proper pair alignments, to generate data comparable to that produced by the Broad Genomics Platform, then we must mark secondary alignments [[GATK discussion forum](https://gatkforums.broadinstitute.org/gatk/discussion/6483/how-to-map-and-clean-up-short-read-sequence-data-efficiently#step3)].

Additionally we will specify:

* the path to genome indexes including prefix
* FASTQ files for paired-end reads
* `2>`: save standard error to file
* `>`: save alignment output to a SAM file

 >**NOTE:** BWA will soft-clip poor quality sequences from the ends of the reads by default, so we do not need to specify a parameter to perform soft clipping.

```bash
$ bwa mem -M -t 2 \
reference_data/chr20 \
raw_data/na12878_1.fq raw_data/na12878_2.fq \
2> logs/bwa.err \
> results/bwa/na12878.sam
```

### Alignment clean-up

The last stage of the alignment phase is marking duplicates, and it is usually only required for variant calling. We need to find reads that are likely artifacts from the PCR amplification as they can bias variant calls.

![align_cleanup](../img/workflow_cleanup.png)

If duplicates aren't marked, then the PCR-based errors will be picked up again and again as false positive variant calls. Duplicates are easy to detect: since they have the same mapping information and CIGAR string:  

![dedup1](../img/dedup_begin.png)

Marking duplicates with tools such as *Picard* or *samblaster* will result in the variant caller ignoring these PCR-based errors, and instead seeing:

![dedup1](../img/dedup_end.png)

The variant caller will be more likely to discard the error, instead of calling it as a variant.

We will be using the [Picard](http://broadinstitute.github.io/picard/) suite of tools from the Broad Institute to sort the alignment SAM file and mark duplicates. The documentation for the tools and their usage and options is available in the [user manual](http://broadinstitute.github.io/picard/command-line-overview.html#Tools).

Using the Picard suite on O2 is a little different from tools we have used this far, let's see what information module spider shows us: 

```bash
$ module spider picard
```

In addition to usual information it gives some information about how to use it. 

```
To use, type
      java -jar $PICARD/picard-2.8.0.jar [options]
```

Java tools usually have a `.jar` executable file and it needs to be run using `java -jar` as well as the full path to the executable file. *You can check what is stored in the `$PICARD` environment variable, before and after you load the module.*

```bash
$ module load picard/2.8.0
```

Let's check what options or specific tools are available to us with *Picard*:

```bash
$ java -jar $PICARD/picard-2.8.0.jar
```

#### Sorting SAM by coordinates

The *Picard* tool, `SortSam`, sorts an input SAM or BAM file by coordinate, queryname, etc. Input and output formats (SAM or BAM) are determined by the file extension.

The description of base options for the `SortSam` tool:

* `INPUT`:	The BAM or SAM file to sort. Required.
* `OUTPUT`:	The sorted BAM or SAM output file. Required.
* `SORT_ORDER`:	Sort order of output file Required. Possible values: {unsorted, queryname, coordinate, duplicate}
* `VALIDATION_STRINGENCY`: Validation stringency for all SAM files read by this program. Possible values: {STRICT, LENIENT, SILENT}
	
> **NOTE:** BWA can produce SAM records that are marked as unmapped but have non-zero MAPQ and/or non-"*" CIGAR. Typically this is because BWA found an alignment for the read that hangs off the end of the reference sequence. Picard considers such input to be invalid. In general, this error can be suppressed in Picard programs by passing VALIDATION_STRINGENCY=LENIENT or VALIDATION_STRINGENCY=SILENT [[3](https://sourceforge.net/p/picard/wiki/Main_Page/)]. 

```bash
$ cd results/bwa

$ java -Xmx8G -jar $PICARD/picard-2.8.0.jar SortSam \
INPUT=na12878.sam \
OUTPUT=na12878_sorted.sam \
SORT_ORDER=coordinate \
VALIDATION_STRINGENCY=SILENT
```

#### Marking duplicates
The *Picard* tool, `MarkDuplicates`, can locate and tag duplicate reads (both PCR and optical/sequencing-driven) in a BAM or SAM file, where duplicate reads are defined as originating from the same original fragment of DNA. Explanation of the process of determining duplicate reads is provided in the [user manual](http://broadinstitute.github.io/picard/command-line-overview.html#Tools).

The basic options for marking duplicates are:

* `INPUT`:	The sorted BAM or SAM file to sort. Required.
* `OUTPUT`:	The BAM or SAM output file. Required.
* `METRICS_FILE`: File to write duplication metrics to Required.
* `ASSUME_SORTED`: If true, assume that the input file is coordinate sorted even if the header says otherwise. Default value: false. Possible values: {true, false}
* `VALIDATION_STRINGENCY`: Validation stringency for all SAM files read by this program. Default value: STRICT. Possible values: {STRICT, LENIENT, SILENT}

```bash
$ java -Xmx8G -jar $PICARD/picard-2.8.0.jar MarkDuplicates \
INPUT=na12878_sorted.sam \
OUTPUT=na12878_sorted_marked.bam \
METRICS_FILE=metrics.txt \
ASSUME_SORTED=true \
VALIDATION_STRINGENCY=SILENT
```

> We use `java -Xmx8G` in the command above to make sure that Java stays within the memory limits we have asked SLURM for. If you are marking duplicates in a large file, it is not unheard of to set up your script or interactive session with over 40G of memory.

#### Creating index for BAM file

Now that we have a sorted BAM file that has duplicates marked, let's index it for visualization with IGV. As we have done in previous sessions, we will use *Samtools* to create the index. We will first need to the load the module:

```bash
$ module load gcc/6.2.0 samtools/1.9

$ samtools index na12878_sorted_marked.bam
```

***
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
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
