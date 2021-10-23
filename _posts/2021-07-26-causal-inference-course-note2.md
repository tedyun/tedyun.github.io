---
layout: post
title: "Causal inference course note - Week 2"
math: true
published: true
last_modified_at: 2021-10-23 0:00:00 +0000
---

This is my note for the "_A Crash Course in Causality: Inferring Causal Effects from Observational Data_" course by [Jason A. Roy](https://sph.rutgers.edu/concentrations/biostatistics-epidemiology/faculty-member.php?id=97108) on [Coursera](https://www.coursera.org/learn/crash-course-in-causality).

---

**Table of Contents**

* Table of contents.
{:toc}

---

# Week 2. Confounding and Directed Acyclic Graphs (DAGs)

## Confounding

We are interested in the relationship between means of different potential outcomes, e.g. $E(Y^1 - Y^0)$. To get this from observational data, we make several assumptions, including ignorability:

$$Y^0, Y^1 \indep A ~\vert~ X$$

Suppose treatment assignment depends on the potential outcomes (e.g. "sicker" patients are more likely to be treated). In that case:
* Treated patients higher risk of bad outcomes.
* Need to account for these pre-treatment differences in health.
  * Suppose $X$ are **measures of health**: history of various of various diseases, age, weight, smoking, alcohol, etc.
  * **Within levels of $X$** (i.e. people of the same age, weight, etc.), it might be the case the "sicker" patients are *not* more likely to get treatment. This is ignorability.

**Confounders** are often defined as variables that affect treatment and affect the outcome.

Examples:
* If treatment was assigned based on a coin flip, then that affects treatment but should not affect outcome. So the coin flip is _not_ a confounder.
* If people with a family history of cancer are more likely to develop cancer (the outcome), but family history was not a factor in the treatment decision, then family history is _not_ a confounder.
* If older people are at higher risk of cardiovascular disease (the outcome) and are more likely to receive statins (the treatment), then age _is_ a confounder.

### Confounder control

We are interested in:
1. Identifying a set of variables $X$ that will make the ignorability assumption hold. If we do this, then $X$ is **sufficient to control for confounding**.
2. Using statistical methods to control for these variables and estimate causal effects (later in the course).

### Causal graphs

Which variables to control for is not a simple question. We'd like to identify a set of variables $X$ that will achieve ignorability - i.e. a set that is sufficient to control for confounding. Causal graphs will help answer this question and formalize the key ideas.

## Causal graphs

### Motivation

Graphs (causal graphs or directed acyclic graphs) are considered useful for causal inference.
* Helpful for identifying which variables to control for.
* Makes assumptions explicit.

In this lecture we will present basic information of graphical models.

### Simple graphs

$A \longrightarrow Y$ is a _directed graph_, which shows that $A$ affects $Y$. The direction of the arrow shows that $A$ affects $Y$.

$A ~ --- ~ Y$ is an _undirected graph_, which shows that $A$ and $Y$ are associated with each other.

### Graphical models
* Encode assumptions about relationships among variables. Graphs tells us which variables are independent, conditionally independent, etc.
* Can be used to derive nonparametric causal effect estimators (later in the course).

### Terminology

$$A \longrightarrow Y$$

* This shows that $A$ affects $Y$.
* $A$ and $Y$ are called **nodes** or **vertices** (we can think of them as variables).
* The link (**edge**) between $A$ and $Y$ is an arrow, which means there's a direction.
* This is a **directed graph**, because all links between variables are directed.
* Variables connected by an edge are **adjacent**.
* A **path** is a way to get from one vertex to another, traveling along edges.

### Directed acyclic graphs (DAGs)

A graph is a **directed acyclic graph (DAG)** if it has no undirected edges and no cycles.

* More terminology in DAGs: **parents**, **children**, **ancestors**, **descendants**.

Ultimately, we will use DAGs to help us determine the set of variables that we need to control for to achieve ignorability. Before we get there, we need to understand the relationship between DAGs and probability distributions.

### Relationship between DAGs and probability distributions

DAGs encode assumptions about dependencies between nodes/variables. Specifically a DAG will tell us:
* which variables are _independent_ from each other.
* which variables are _conditionally independent_ from each other.
* ways that we can factor and simplify the joint distribution.

Example 1.

$$D \rightarrow A \rightarrow B ~~~~~ C$$

A DAG involving nodes $A$, $B$, $C$, and $D$ encodes assumptions about the joint distribution $P(A, B, C, D)$. This DAG implies:
* $P(C \vert A, B, D) = P(C)$, i.e. $C$ is independent of all variables.
* $P(B \vert A, C, D) = P(B \vert A)$, i.e. $B \indep D, C ~\vert~ A$
* $P(B \vert D) \neq P(B)$, i.e. $B$ and $D$ are (marginally) dependent.
* $P(D \vert A, B, C) = P(D \vert A)$.

### Decomposition of joint distribution

We can decompose the joint distribution by sequential conditioning only on sets of parents.
1. Start with _roots_ (nodes with no parents).
2. Proceed down the descendant line, always conditioning on parents.

Example.

$$D \rightarrow A \rightarrow B ~~~~~ C$$

The decomposition based on this DAG is: $P(A, B, C, D) = P(C) P(D) P(A \vert D) P(B \vert A)$


### Compatibility between DAGs and Distributions

A DAG admits a **factorization** of probability distribution as shown above. We say that this probability function and this DAG are **compatible**.

Example. The following DAG and the probability function are compatible.
* $A \leftarrow D \rightarrow B \rightarrow C$, $A \rightarrow C$
* $P(A, B, C, D) = P(D)P(A \vert D) P(B \vert D) P(C \vert A, B)$

Note that DAGs that are compatible with a particular probability function are not necessarily unique.

Simple example: $A \rightarrow B$ and $B \rightarrow A$ both convey that $A$ and $B$ are dependent.

## Paths and associations

### Types of paths

* **Forks**: $D \leftarrow E \rightarrow F$
* **Chains**: $D \rightarrow E \rightarrow F$
* **Inverted forks**: $D \rightarrow E \leftarrow F$

### When do paths induce associations?

If nodes $A$ and $B$ are on the ends of a path, they are associated (via this path) if:
* Some information flows to both of them.
* Information from one makes it to the other.

In a fork, information flows to both ends of the fork. In a chain, information from one makes it ot the other.

However, in an _inverted fork_ $A \rightarrow G \leftarrow B$, information from $A$ and $B$ collide at $G$. Here $G$ is known as a **collider**. In this case, $A$ and $B$ both affect $G$ but information does not flow from $G$ to either $A$ or $B$. Hence, $A$ and $B$ are independent (if this was the only path between them).

More generally, if there is a collider anywhere on a path from $A$ to $B$, then no association between $A$ and $B$ comes from that path.

## Conditional independence (d-separation)

### Blocking

Paths can be **blocked** by _conditioning_ on nodes in the path.

Consider the path: $A \rightarrow G \rightarrow B$. If we condition on $G$ (a node in the middle of a chain), we block the path from $A$ and $B$.

Association on a fork can also be blocked. Consider the path: $A \leftarrow G \rightarrow B$. If we condition on $G$, this path from $A$ to $B$ is blocked.

The opposite situation occurs if a <u>collider is conditioned on</u>. Consider the path: $A \rightarrow G \leftarrow B$. Here $A$ and $B$ are not associated via this path (information collides at $G$). However, <u>conditioning on $G$ induces an association between $A$ and $B$</u>.

### Conditioning on colliders

Suppose that
* $A$ is the state of an on/off switch.
* $B$ is the state of a different on/off switch.
* $G$ is whether the lightbulb is lit up.
* $A$ and $B$ are determined from separate, independent coin flips. $G$ is lit up only if **both** $A$ and $B$ are in the on state.

Then,
* $A$ and $B$ are (marginally) independent.
* $A$ and $B$ are dependent _given_ $G$. If I tell you that the light is off ($G$), then $A$ must be off if $B$ is on, and vice versa.

### d-separation

A path is **d-separated** (here "d" means dependency) by a set of nodes $C$ if:
* it contains a chain and the middle part is in $C$, **or**
* it contains a fork and the middle part is in $C$, **or**
* it contains an inverted fork and the middle part is not in $C$, nor are any descendants of the middle part.

Two nodes, $A$ and $B$, are **d-separated** by a set of nodes $C$ if it _blocks every path_ from $A$ to $B$. Then,

$$A \indep B ~\vert~ C$$

Recall the ignorability assumption: $Y^0, Y^1 \indep A ~\vert~ X$. Our goal is to identify a set of variables $X$ that will create this conditional independence.

## Confounding revisited

### Confounders

Recall that the informal definition of a confounder is: "a variable that affects both the treatment and the outcome". This is a simple DAG where $X$ is a confounder between the relationship between treatment $A$ and outcome $Y$:

$$X \rightarrow A \rightarrow Y, ~ X \rightarrow Y$$

### Controlling for confounding

We want to identify a set of variables that are sufficient to control for confounding. To do this, we need to block _backdoor paths_ from treatment to outcome. We'll learn what that means.

### Frontdoor paths

A **frontdoor path** from $A$ to $Y$ is one that begins with an arrow emanating out of $A$.

Example 1. $X \rightarrow A \rightarrow Y,~ X \rightarrow Y$
* $A \rightarrow Y$ is a frontdoor path from $A$ to $Y$
* Here $A$ directly affects $Y$

Example 2. $X \rightarrow A \rightarrow Z \rightarrow Y,~ X \rightarrow Y$
* $A \rightarrow Z \rightarrow Y$ is a frontdoor path from $A$ to $Y$.
* Here $A$ affects $Y$ indirectly through its effect on $Z$.

If we are interested in the causal effect of $A$ on $Y$, we should _not_ control for $Z$ in Example 2. We care about the question "if we manipulate $A$, how is $Y$ affected?" Controlling for $Z$ would be controlling for an effect of treatment.

Note. _Causal mediation analysis_ involves understanding frontdoor paths from $A$ to $Y$. This will not be covered in this course.

### Backdoor paths

**Backdoor paths** from treatment $A$ to outcome $Y$ are paths from $A$ to $Y$ that travel through arrows going into $A$.

Example. $X \rightarrow A \rightarrow Y,~ X \rightarrow Y$
* Here, $A \leftarrow X \rightarrow Y$ is a backdoor path from $A$ to $Y$.

Backdoor paths confound the relationship between $A$ and $Y$. These need to be blocked.

To sufficiently control for confounding, we must identify a set of variables that block all backdoor paths from treatment to outcome. If $X$ is this set of variables, then we have $Y^0, Y^1 \indep A ~\vert~ X$, i.e. ignorability of treatment mechanism given $X$.

In the next lectures, we will discuss two criteria for identifying sets of variables that are sufficient to control for confounding:
* Backdoor path criterion
* Disjunctive cause criterion

## Backdoor path criterion

### Sufficient sets of confounders

A set of variables $X$ is sufficient to control for confounding if:
* it blocks all backdoor paths from treatment to the outcome
* it does not include any descendants of treatment

This is the **backdoor path criterion**.

Example 1. $V \rightarrow A \rightarrow Y,~ V \rightarrow W \rightarrow Y$
* There is one backdoor path from $A$ to $Y$ ($A \leftarrow V \rightarrow W \rightarrow Y$).
* It is not blocked by a collider.
* Sets of variables that are sufficient to control for confounding: $\\{V\\}$, $\\{W\\}$, or $\\{V, W\\}$.

Example 2. $V \rightarrow A \rightarrow Y,~ V \rightarrow M \leftarrow W \rightarrow Y$.
* There is one backdoor path from $A$ to $Y$, but it is blocked by a collider $M$. So no confounding.
* However, if $M$ is controlled for, it opens a path between $V$ an $W$.
* Sets of variables that are sufficient to control for confounding: $\\{\\}$, $\\{V\\}$, $\\{W\\}$, $\\{M, V\\}$, $\\{M, W\\}$ or $\\{M, V, W\\}$. (But _not_ $\\{M\\}$.)

For a real example of a causal DAG, see Figure 1 in [Vahratian et al. 2005](https://doi.org/10.1016/j.annepidem.2005.02.005) for example (treatment = "maternal pre-pregnancy overweight and obesity", outcome = "cesarean delivery").

## Disjunctive cause criterion

### Variable selection

One method for choosing variables to control for is the **disjunctive cause criterion** ([VanderWeele & Shpitser 2011](https://doi.org/10.1111/j.1541-0420.2011.01619.x)) The criterion is simple:
* Control for all (observed) causes of the exposure, the outcome, or both.

Investigators do not need to know the whole graph, but rather, the list of variables that affect exposure or outcome.

If there is a set of observed variables that satisfy the backdoor path criterion, then <u>the variables selected based on the disjunctive cause criterion will be sufficient to control for confounding</u>.


### Examples

Assume the following:
* Observed pre-treatment variables: $\\{M, W, V\\}$.
* Unobserved pre-treatment variables: $\\{U_1, U_2\\}$.
* $W$ and $V$ are causes of $A$, $Y$, or both.
* $M$ is not a cause of either $A$ or $Y$.

Let's compare two methods for selecting variables in various hypothetical DAGs:
1. Use _all_ pre-treatment covariates: $\\{M, W, V\\}$ in this example.
2. Use variables based on disjunctive cause criterion: $\\{W, V\\}$ in this example.

<u>Hypothetical DAG 1</u>. $V \rightarrow A \rightarrow Y,~ M \leftarrow V \rightarrow W \rightarrow Y$
1. Use all pretreatment covariates $\\{M, W, V\\}$.
   * Satisfies backdoor path criterion? YES.
2. Use variables based on disjunctive cause criterion: $\\{W, V\\}$.
   * Satisfies backdoor path criterion? YES.

<u>Hypothetical DAG 2</u>. $V \rightarrow A \rightarrow Y,~ V \rightarrow M \leftarrow W \rightarrow Y$
1. Use all pretreatment covariates $\\{M, W, V\\}$.
   * Satisfies backdoor path criterion? YES.
2. Use variables based on disjunctive cause criterion: $\\{W, V\\}$.
   * Satisfies backdoor path criterion? YES.

<u>Hypothetical DAG 3</u>.
$W \rightarrow A \rightarrow Y \leftarrow V,~ A \dashleftarrow U_1 \dashrightarrow M \dashleftarrow U_2 \dashrightarrow Y$ (Dashed arrows mean unobserved.)
1. Use all (observed) pretreatment covariates $\\{M, W, V\\}$.
   * Satisfies backdoor path criterion? **NO**.
2. Use variables based on disjunctive cause criterion: $\\{W, V\\}$.
   * Satisfies backdoor path criterion? YES.

<u>Hypothetical DAG 4</u>.
$M,~ W \dashleftarrow U_1 \dashrightarrow A \rightarrow Y \leftarrow V,~ W \dashleftarrow U_2 \dashrightarrow Y,~ W \rightarrow Y$ (Dashed arrows mean unobserved.)
1. Use all (observed) pretreatment covariates $\\{M, W, V\\}$.
   * Satisfies backdoor path criterion? **NO**.
2. Use variables based on disjunctive cause criterion: $\\{W, V\\}$.
   * Satisfies backdoor path criterion? **NO**.

### Summary

The disjunctive cause criterion:
* does not always select the smallest set of variables to control for,
* but it is conceptually simpler,
* is guaranteed to select a set of variables that are sufficient to control for confounding **if**
  * such a set exists,
  * we correctly identified all observed causes of $A$ and $Y$.

Once we know which variables to control for, the question now is _how_ to control for them. General approaches include: **matching** and **inverse probability of treatment weighting**. We will discuss these later in the course.
