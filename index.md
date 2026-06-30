---
title: Standard Format
layout: home
nav_order: 6
---

# The standard linked-read data format
## Rationale
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

## Standard Format
![](../assets/images/lr_standard.png)
- `BX:Z` tag to record the barcode, the format of which is irrelevant
  - it could be haplotagging `ACBD`, stLFR `1_2_3`, nucleotides, _whatever_
- `VX:i` tag to record if the corresponding barcode is valid
  - `0` is invalid
  - `1` is valid
- Uses older CASAVA format (`/1` or `/2`) to identify forward/reverse sequence
- Other SAM-style comments are allowed
- Comments are tab-separated

This format makes it the responsibility of the early data processing software 
(as early as demultiplexing) to encode the data in this format for downstream
processing. Our hope is that this htslib-compliant format is adopted to reduce
the fragmentation in the software ecosystem and reduce the need for file format
conversions. It also creates a blueprint for a generic file encoding for any new
linked-read methods that are being developed or can be developed in the future.

### FASTQ example
stLFR style FASTQ header with a valid barcode
```
@A00470:481:HNYFWDRX2:1:2101:29532:1063/1 BX:Z:45_11_361 VX:i:1
```

haplotagging style FASTQ header with an invalid barcode
```
@A00470:481:HNYFWDRX2:1:2101:29532:1063/1 BX:Z:A78C00B14D96 VX:i:0
```
### SAM/BAM example
haplotagging style SAM record with a valid barcode
```
A00470:481:HNYFWDRX2:1:2229:29912:29778 99      2R      1222    40      19S80M  =   1500     428     AGATGTGTATAAGAGACAGAGTTATGTCATTTTAAGCGGTCAAAATGGGTGAATTTCCGATTTCAAGTAATAGGCGAACTCAAGATACCTTCTACAGAT  FFFFFFFFFFFFFFFFF:FFFFF:FFFFF:FFFF:FFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:,FFFFFFF:FFFFF  NM:i:0  MD:Z:80 MC:Z:150M       AS:i:80      XS:i:80 RG:Z:sample1    MI:i:1  VX:i:1  BX:Z:A55C67B91D96
```

nucleotide (TELLseq/10X) style SAM record with an invalid barcode
```
A00470:481:HNYFWDRX2:1:2229:29912:29778 99      2R      1222    40      19S80M  =   1500     428     AGATGTGTATAAGAGACAGAGTTATGTCATTTTAAGCGGTCAAAATGGGTGAATTTCCGATTTCAAGTAATAGGCGAACTCAAGATACCTTCTACAGAT  FFFFFFFFFFFFFFFFF:FFFFF:FFFFF:FFFF:FFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:,FFFFFFF:FFFFF  NM:i:0  MD:Z:80 MC:Z:150M       AS:i:80      XS:i:80 RG:Z:sample1    MI:i:1  VX:i:0  BX:Z:TACANNNCACAGAG
```

## HTSlib Compliance
This format was carefully chosen for broad compatibility and seamless integration with HTSlib tools (e.g. `samtools`, `bcftools`).
It's also spec-compliant with the `htslib` software API, which sets the standard for other APIs like `biogo/hts` (Go), `pysam` (Python), and `rust-bio/rust-htslib` (Rust). 
Using the older `/1` CASAVA format for forward/reverse encoding ensures any and all comment fields can be transferred from FASTQ
to SAM in aligners that allow comment-transfer (e.g., `bowtie`, `bwa`, `strobealign`). The combination of the new CASAVA format and FASTQ
comments break this behavior.

## Unified Invalidation
Each of the existing linked-read techonologies represents bad (invalid) barcodes a different way. By using a `VX:i` tag,
barcode validity is always known, regardless of the barcode format.

## Infinitely Extensible
The combination of `BX:Z` and `VX:i` tags create a future-proof format that allows existing technologies to change
their formats without breaking backwards compatibility (e.g., stLFR adding a 4th segment). It also means new
linked-read technologies can hit the ground running with whatever new barcode format they would like and software/pipelines
that accept the Standard format will be compatible out of the box. 
