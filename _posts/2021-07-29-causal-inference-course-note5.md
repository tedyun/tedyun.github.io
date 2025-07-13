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

### Setup

Randomize trial:
* $Z$: randomization to treatment (1 if randomized to treatment, 0 otherwise).
* $A$: treatment received (1 if receive treatment, 0 otherwise).
* $Y$: outcome.
* Note: Typically, not everyone assigned to treatment will actually receive the treatment (non-compliance).

### DAG

* Non-compliance makes a randomized trial like an observational study.
* There could be confounding based on _treatment received_.
  * Common causes of treatment received and the outcome.
* It might be reasonable to assume that treatment assignment does not directly affect Y.

$$Z \rightarrow A \rightarrow Y \leftarrow X \rightarrow A$$

### Potential treatment

* Observed data: $(Z, A, Y)$.
* For a given subject, they were assigned treatment $Z$ and received treatment $A$.
  * Their treatment received might have been different had they been assigned treatment $1 - Z$.
* Each subject as two _potential values of treatment_:
  * $A^{Z=1} = A^1$: Value of treatment if randomized to $Z=1$.
  * $A^{Z=0} = A^0$: Value of treatment if randomized to $Z=0$.

### Causal effect of assignment on receipt

We can think of the average causal effect of treatment _assignment_ on treatment _received_ as:

$$E(A^1 - A^0)$$

* This is proportion treated if everyone had been assigned to received treatment, minus the proportion treated if no one had been assigned to receive the treatment.
  * If perfect compliance, this would be equal to 1.
* This is generally _estimable from the observed data_, as, by randomization and consistency:
  * $E(A^1) = E(A \vert Z = 1)$
  * $E(A^0) = E(A \vert Z = 0)$

### Causal effect on assignment on outcome

We can think of the average causal effect of treatment _assignment_ on the _outcome_ as:

$$E(Y^{Z=1} - Y^{Z=0})$$

* This is average values of the outcome if everyone had been assigned to received treatment, minus the average outcome if no one had been assigned to receive the treatment.
  * Intention-to-treat effect.
  * If perfect compliance, this would be equal to the causal effect of treatment.
* This is generally _estimable from the observed data_, as, by randomization and consistency:
  * $E(Y^{Z=1}) = E(Y \vert Z=1)$
  * $E(Y^{Z=0}) = E(Y \vert Z=0)$

### Causal effect of treatment

What about the causal effect of treatment received on the outcome?
* $Z$ can be thought of as (strong) _encouragement_ to receive the treatment.
  * It's an IV. Does this help us estimate causal effects of treatment?
    * This will be explored in another video.

## Compliance classes

### Potential values of treatment

We can classify people based on potential treatment.

| $A^0$ | $A^1$ | Label |
| - | - | - |
| 0 | 0 | Never-takers |
| 0 | 1 | Compliers |
| 1 | 0 | Defiers |
| 1 | 1 | Always-takers |

[Angrist, Imbens, & Rubin, JASA, 1996](http://doi.org/10.1080/01621459.1996.10476902).

### Subpopulations

We can think of these as subpopulations of people:
* Never-takers: Do not take treatment, regardless of randomization (encouragement does not work). We would not learn anything about the effect of treatment in this subpopulation, as there is no variation in treatment received.
* Compliers: Take treatment when encouraged to, and do not otherwise. In this group, treatment received is randomized.
* Defiers: Do the opposite of what they are encouraged to do. In this group treatment received is also randomized, but in the opposite way.
* Always-takers: Always take treatment. In this group there is no variation in treatment received. No information about causal effect.

### Causal effects

A motivation for using IV methods in general is concern about possible _unmeasured confounding_.
* If there is unmeasured confounding, _cannot marginalize over all confounders_ (via matching, IPTW, etc).

IV methods do not focus on the average causal effect for the population.
* They focus on a _local average treatment effect_.

### Local average treatment effect

In IV methodcs, the _target of inference_ is:

$$E(Y^{Z=1} \vert A^0 = 0, A^1 = 1) - E(Y^{Z=0} \vert A^0 = 0, A^1 = 1)$$

$$= E(Y^{Z=1} - Y^{Z=0} \vert \text{compliers})$$

$$= E(Y^{A=1} - Y^{A=0} \vert \text{compliers})$$

This is causal because it _contrasts counterfactuals_ in a _common population_.
Known as **complier average causal effect (CACE)**.
* This is a causal effect in a subpopulation.
* A "local" causal effect.
* No inference about defiers, always-takers, or never-takers.

### Observed data

For each person we observe an $A$ and a $Z$, not ($A^0$, $A^1$).

| $Z$ | $A$ | $A^0$ | $A^1$ | Class |
| - | - | - | - | - |
| 0 | 0 | 0 | ? | Never-takers or compliers |
| 0 | 1 | 1 | ? | Always-takers or defiers |
| 1 | 0 | ? | 0 | Never-takers or defiers |
| 1 | 1 | ? | 1 | Always-takers or compliers |

Without additional assumptions, we cannot classify each subject into one of these categories. We can narrow it down to two options, however.

### Identifiability

Compliance classes above are also known as _principal strata_. These are _latent_ (not directly observable).

How can we _estimate_ the complier average causal effect? What _assumptions_ are needed? These are topics for other lectures.

## Assumptions

### Assumptions about IVs

A variable is an instrumental variable (IV) if:

1. It is associated with the treatment.
2. It affects the outcome only through its effect on treatment (known as the _exclusion restriction_).

$$Z \rightarrow A \rightarrow Y$$
$$A \leftarrow X \rightarrow Y$$

If $Z$ is an IV, $Z$ must not _directly_ affect $Y$ (exclusion restriction).

When there is unmeasured confounding,

$$Z \rightarrow A \rightarrow Y$$
$$A \leftarrow X \rightarrow Y$$
$$A \dashleftarrow U \dashrightarrow Y$$

$Z$ must not affect unmeasured confounders $U$. $Z$ cannot directly, or indirectly through its effect on $U$, affect the outcome $Y$.

### Realistic?

IF $Z$ is random treatment assignment, are the IV assumptions met?

* It should affect treatment received. (We can check this.)
* If we think of it as a coin flip, then it should not affect the outcome or unmeasured confounders.
  * However, if subjects are not blinded, knowledge of what they were assigned to could affect them.
  * If clinicians are not blinded to assignment, it could affect them.
  * Need to examine this assumption carefully for any given study.

### Causal effects

Assuming we have a valid IV, we are interested in using it to help us estimate the complier average causal effect:

$$E(Y^{A=1} - Y^{A=0} \vert \text{compliers})$$

The causal effect of treatment among subjects who only take treatment if they are randomized to $Z=1$.

### Identification challenge

Challenge: We are interested in compliers, but we do not know who the compliers are.

### Monotonicity assumption

The **monotonicity assumption** is there are _no defiers_.
* No one consistently does the opposite of what they are told.
* It is called monotonicity because the assumption is that the probability of treatment should increase with more encouragement.

With monotonicity,

| $Z$ | $A$ | $A^0$ | $A^1$ | Class |
| - | - | - | - | - |
| 0 | 0 | 0 | ? | Never-takers or compliers |
| 0 | 1 | 1 | ~~?~~ 1 | Always-takers ~~or defiers~~ |
| 1 | 0 | ~~?~~ 0 | 0 | Never-takers ~~or defiers~~ |
| 1 | 1 | ? | 1 | Always-takers or compliers |
