---
layout: post
title: "Causal inference course note"
math: true
published: true
last_modified_at: 2021-07-25 0:00:00 +0000
---

This is my note for the "_A Crash Course in Causality: Inferring Causal Effects from Observational Data_" course by [Jason A. Roy](https://sph.rutgers.edu/concentrations/biostatistics-epidemiology/faculty-member.php?id=97108) on [Coursera](https://www.coursera.org/learn/crash-course-in-causality).

## Week 3

### Propensity score

The _propensity score_ is the probability of receiving treatment (rather than control), given covariates $X$. Propensity score $\pi_i$ for a subject $i$ is defined by:

$$\pi_i := P(A = 1 \vert X_i)$$

Propensity score is a _balancing score_, i.e. if you restrict to a subpopulation of subjects who have the same value of the propensity score, there should be balance in the two treatment groups. More formally,

$$P(X=x \vert \pi(X)=p, A=1) = P(X=x \vert \pi(X)=p, A=0).$$

This can be proved using Bayes' theorem. This implies that if we match on the propensity score, we should achieve balance. Note that conditioning on the propensity score is conditioning on an **allocation probability**.

In a randomized trial, the propensity score is generally known, e.g. 0.5. In an observational study, it will be unknown, but we estimate it given the observed data. We estimate the score $P(A=1 \vert X)$ by, for example, fitting a logistic regression model with covariates $X$ and outcome $A$. After fitting the model, the estimated propensity score is the predicted probability (fitted value) for each subject.
