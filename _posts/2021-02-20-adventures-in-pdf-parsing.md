---
author: worldwise001
layout: post
title: Adventures in PDF Parsing
---

I've been on and off exploring text extraction from PDFs, specifically from CS academic papers, for reasons I'll explain in a future blog post. The main goals of my text extraction quest were to extract specific components, but the reliability of extraction could vary depending of the importance of the component. Required components were:
* Title
* Author Bios (name, affiliation, email)
* Section and subsection headers
* Main text section/subsection content
* References/Bibliography

Some components I recognized were going to be harder to get reliably, but 
