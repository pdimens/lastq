+++
title= "Standard / LASTQ"
weight= 1
+++
 
![](/img/lr_standard.png)

## Specification
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