---
title: Haplotagging
layout: home
---

# Haplotagging
Unlike the other listed chemistries, haplotagging is a non-commercial (DIY) linked-read chemistry. Haplotagging barcodes are combinatorial and
are made up of four 6bp segments. Two of these segments ("A" and "C") are the first 12bp of the $I1$ read and
the other two ("B" and "D") are the first 12bp of the $I2$ read, both of which are provided by Illumina for standard sequencing runs.
Because of this segment design, there are $96^4$ (~84 million) possible barcode combinations (~900,000 per sample). 
The barcodes are stored in the sequence header under the `BX:Z` SAM tag, recoded in their "`ACBD`" format.

![Haplotagging data format](/static/lr_haplotagging.svg)

- 4 barcode segments
  - `A` segment is the first 6bp of the $I1$ read
  - `C` segment is the next 6bp of the $I1$ read (7-12)
  - `B` segment is the first 6bp of the $I2$ read
  - `D` segment is the next 6bp of the $I2$ read (7-12)
- barcode stored as `BX:Z` tag in the read header in `ACBD` format
  - e.g. `@A003432423434:1:324 BX:Z:A45C01B84D21`
- invalid barcode segment encoded with `00` (e.g., `C00`)
