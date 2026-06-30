+++
title= "TELLseq"
weight=6
+++

One of the presently available commercial linked-read options. TELLseq data is very similar to 10X, except the
barcode is 18bp long and contained in the `I1` read that Illumina provides with the standard `R1` and `R2` reads. The
barcode gets appended in the read header using a colon (`:`).

![](/img/lr_tellseq.png)

## Specification

- barcode is the first 18bp of the I1 read 
- barcode is appended to sequence header
  - e.g. `@A00234534562:1:544:AATTATACCACAGCGGTA`
- invalid barcode contains at least one `N` character
- advertised to have a capacity over 2 **billion** barcodes, but realistically use <24 **million**
