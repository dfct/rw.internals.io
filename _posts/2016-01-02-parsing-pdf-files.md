---
layout: post
title: "Parsing PDF Files"
tags:
- pdf
- parsing
---


Earlier in the year I spent some time looking for PDF parsing solutions. If you're ever stuck working with a PDF file with no easy access to the data that generated it, you may need to parse meaningful information out of the PDF itself. This is difficult due to how PDFs are rendered.

Long story short: commercial solutions can knock this out of the park by converting the PDF to a parseable format such as csv, maintaining the structure of the document. At the time I was researching this, free solutions could convert the document to a parseable format but could not maintain the document structure, leaving you with a jumble of text strings.

- - - -

The rest of the story, aka the notes I had:

* Commercial PDF parsing solutions maintain text fidelity, including tables, and convert to many formats including csv & xls. With that, were it your need, you can pull out actual objects like a table by parsing a converted file.
* Free PDF parsing solutions largely only pull out streams of text. Formatting is lost, and you would be working with string comparisons. For tables in particular, this makes it very difficult to parse.
	* Tabula is the only free and open source product I found that could pull tables, but has fidelity problems.
* The downside of a commercial solution is cost – they run around $1000 for a license.
* The fidelity maintained during conversion varies by solution. Some end up merging columns in a table due to their close physical proximity.
* ByteScout was a clear favorite among the internets. It has high fidelity, is predictable in its parsing, and has a solid SDK.

**A Non-Exhaustive But Solid List of Commercial Solutions with Support for Tables:**

* ByteScout
* Spire
* Aspose
* SautinSoft
* A-PDF
* Bitmiracle
* Microsoft Word
* Adobe Acrobat

**One Free Solution with Support for Tables**

* Tabula

**Text Only Solutions**

* iTextSharp
* PdfSharp
* IFilter
* PDFBox
* Toxy (uses PDF Sharp)
* Tika on DotNet
* PDF Clown
