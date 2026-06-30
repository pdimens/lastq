+++
title= "Standard linked-read data format"
sort_by = "weight"
template = "section.html"
+++

<img style="float: right;" alt="" src="img/logo.png" />

Shortly after linked-read technology from 10X Genomics debuted in 2016, it was discontinued in 2019.
Since then, new and improved methods (haplotagging, stLFR, TELLseq, etc.) have emerged, each with
a different way of encoding linked-reads. The disagreement between these formats is frustrating at minimum,
and crippling at other times, as different software have different format expectations. 'LASTQ' is the half-joking
abbreviation for what we are calling the Standard Linked Read Format (Linked + FASTQ = LASTQ). The format also
extends to SAM files. Our mission is to make this a standard and default format across linked-read technologies such that they have equal utility across all
tools.

## Linked-read data types
It's worth describing the obvious differences of the raw (FASTQ) data across the existing linked-read types.
Knowing these details might help you make sense of compatibilties/incompatibilities for software, or how you can convert between styles.
The navigation pane goes into more detail about these formats.

| Type         | Location               | Format   | Invalid Encoding | Example                            |
|:-------------|:-----------------------|:---------|:-----------------|:-----------------------------------|
| 10X          | R1 read                | `ATCG`   | `N`              | `AGGTTGGGTAAGATA...`               |
| Haplotagging | `BX:Z` tag             | `ACBD`   | `00` segment     | `BX:Z:A04C54B96D11`                |
| stLFR        | end of sequence ID     | `#1_2_3` | `0` segment      | `@A003432423434:1:324#12_432_1`    |
| TELLseq      | end of sequence ID     | `:ATCG`  | `N`              | `@A003432423434:1:324:TTACCACGAGG` |
| Standard     | `BX:Z` and `VX:i` tags | any      | `VX:i:0`         | `BX:Z:31_442_512 VX:i:1`           |


## Why LASTQ?
### HTSlib Compliance
This format was carefully chosen for broad compatibility and seamless integration with HTSlib tools (e.g. `samtools`, `bcftools`).
It's also spec-compliant with the `htslib` software API, which sets the standard for other APIs like `biogo/hts` (Go), `pysam` (Python), and `rust-bio/rust-htslib` (Rust). 
Using the older `/1` CASAVA format for forward/reverse encoding ensures any and all comment fields can be transferred from FASTQ
to SAM in aligners that allow comment-transfer (e.g., `bowtie`, `bwa`, `strobealign`). The combination of the new CASAVA format and FASTQ
comments break this behavior.

### Unified Invalidation
Each of the existing linked-read techonologies represents bad (invalid) barcodes a different way. By using a `VX:i` tag,
barcode validity is always known, regardless of the barcode format.

### Infinitely Extensible
The combination of `BX:Z` and `VX:i` tags create a future-proof format that allows existing technologies to change
their formats without breaking backwards compatibility (e.g., stLFR adding a 4th segment). It also means new
linked-read technologies can hit the ground running with whatever new barcode format they would like and software/pipelines
that accept the Standard format will be compatible out of the box. 
