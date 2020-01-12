---
layout: post
title: "Headings and Equations"
mathjax: true
---

# First heading

Normal text.

## Second heading

Normal text.

### Third heading

Normal text.

#### Fourth heading

**Hello world**. Inline equations like \\(e^x\\) or $-\log_2 p$ can be rendered by setting `mathjax: true` in the jekyll front matter and using `$..$` or `\\(..\\)` (double backslash is required to as both Jekyll and MathJax processes escapes). For the literal dollar sign \\$, must use `\\$`.

$$ R_{\mu \nu} - \frac{1} {2}Rg_{\mu \nu} + \Lambda g_{\mu \nu} = \frac{8\pi G} {c^4}T_{\mu \nu} $$
