---
layout: post
title: "Causal inference course note - Week 5"
math: true
published: true
last_modified_at: 2021-10-23 0:00:00 +0000
---

This is my note for the "_A Crash Course in Causality: Inferring Causal Effects from Observational Data_" course by [Jason A. Roy](https://sph.rutgers.edu/concentrations/biostatistics-epidemiology/faculty-member.php?id=97108) on [Coursera](https://www.coursera.org/learn/crash-course-in-causality).

---

* [Week 1. Welcome and Introduction to Causal Effects]({% post_url 2021-07-25-causal-inference-course-note1 %})
* [Week 2. Confounding and Directed Acyclic Graphs (DAGs)]({% post_url 2021-07-26-causal-inference-course-note2 %})
* [Week 3. Matching and Propensity Scores]({% post_url 2021-07-27-causal-inference-course-note3 %})
* [Week 4. Inverse Probability of Treatment Weighting (IPTW)]({% post_url 2021-07-28-causal-inference-course-note4 %})
* [Week 5. Instrumental Variables Methods]({% post_url 2021-07-29-causal-inference-course-note5 %})

---

**Table of Contents**

* Table of contents.
{:toc}

---

# Week 5. Instrumental Variables Methods

## Introduction to instrumental variables

### Confounding

Classic confounding situation: $X$ affects treatment $A$ and affects outcome Y: $X \rightarrow A \rightarrow Y \leftarrow X$. If $X$ is observed, we can analyze data using: matching, PS matching, IPTW. Even if there are risk factors, $V$, such that $V \rightarrow Y$, it's valid to simply control for $X$.

### Unmeasured confounding

Suppose there are unmeasured/unobserved variables, $U$, that affect $A$ and $Y$: $A \dashleftarrow U \dashrightarrow Y$. Then we have _unmeasured confounding_.
* Ignorability assumption violated.
* Biased estimates of causal effects.
* Cannot control for confounders and average over the distribution, if we do not observe them all.

### Instrumental variables

Instrumental variables (IV) is an alternative causal inference method that does not rely on the ignorability assumption. Here $Z$ is an IV:

$$Z \rightarrow A \rightarrow Y \leftarrow X \rightarrow A$$

It affects treatment, but does not (directly) affect the outcome. Think of $Z$ as _encouragement_.

### Example

* $A$: somking during pregnancy (yes/no)
* $Y$: birthweight
* $X$: parity, mother's age, weight, etc.

Concern: Could be unmeasured confounders.

Challenge: not ethical to randomly assign smoking to pregnant women.

### Encouragement design

* $A$: somking during pregnancy (yes/no)
* $Y$: birthweight
* $X$: parity, mother's age, weight, etc.
* $Z$: randomize to either receive _encouragement_ to stop smoking ($Z=1$) or receive usual care ($Z=0$).

[Sexton and Hebel, JAMA, 1984](http://doi.org/10.1001/jama.1984.03340310025013).

An intention-to-treat analysis would focus on the _causal effect of encouragement_:

$$E(Y^{Z=1}) - E(Y^{Z=0})$$

This is a valid causal effect and would likely be of some interest.

What can we say about the _causal effect of smoking_ itself? This is the focus of IV methods.

### Instrumental variables

Sometimes the IV is randomly assigned as part of the study.
Other times, the IV is believed to be randomized in nature (natural experiment):
* Mendelian randomization.
* Quarter of birth.
* Geographic distance to specialty care provider.

We will look at both situations in other lectures.

## Randomized trials with noncompliance

[TODO]
