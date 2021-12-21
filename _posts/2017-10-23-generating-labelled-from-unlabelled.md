---
layout: post
title:  "[Unpublished] Generating Labelled Data from Unlabelled Data"
math: true
comments: false
published: false
---

An idea for generating unlabelled data from unlabelled data and two high-performing existing classifier.

1. Collect a big amount of unlabelled data
1. Use two(or maybe more?) high performance existing parsers to parse an unlabelled sentence.
1. If they all agree on dependency parsing on a datum, make it a labelled datum, otherwise discard.

From Tri-Training (Li et al, 2014) for natural language parsing:
https://pdfs.semanticscholar.org/a7d6/85ff8532ecb972c9382f86dea53ee7528264.pdf
