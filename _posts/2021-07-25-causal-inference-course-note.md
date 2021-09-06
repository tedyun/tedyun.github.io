---
layout: post
title: "Causal inference course note"
math: true
published: true
last_modified_at: 2021-09-05 0:00:00 +0000
---

This is my note for the "_A Crash Course in Causality: Inferring Causal Effects from Observational Data_" course by [Jason A. Roy](https://sph.rutgers.edu/concentrations/biostatistics-epidemiology/faculty-member.php?id=97108) on [Coursera](https://www.coursera.org/learn/crash-course-in-causality).

---

**Table of Contents**

* Table of contents.
{:toc}

---

# Week 1

## A brief history

Statisticians started working on causal modeling as far back as the 1920s (Wright 1921; Neyman 1923). It became its own area of statistical research since about the 1970s.

Some highlights:
* Re-introduction of _potential outcomes_; Rubin causal model (Rubin 1974).
* _Causal diagrams_ (Greenland and Robins 1986; Pearl 2000).
* _Propensity scores_ (Rosenbaum and Rubin 1983).
* _Time-dependent confounding_ (Robins 1986; Robins 1997).
* _Optimal dynamic treatment strategies_ (Murphy 2003; Robins 2004).


## Going forward

In this course, we will primarily focus on causal inference from _observational studies_ and _natural experiments_. It is important to remember that:

* Causal inference requires making some untestable assumptions ("_causal assumptions_").
* Cochran (1972) concludes: "..observational studies are an interesting and challenging field which demands a good deal of humility, since we can claim only to be groping toward the truth."


## Potential outcomes and counterfactuals

### Potential outcomes

Suppose we are interested in the causal effect of some treatment $A$ on some outcome $Y$.

Example:
* $A = 1$ if received influenza vaccine; $A=0$ otherwise.
* $Y = 1$ if develop cardiovascular disease within 2 years; $Y = 0$ otherwise.

_Potential outcomes_ are the outcomes we _would_ see under each possible treatment option. $Y^a$ is the outcome that would be observed if treatment was set to $A = a$. When the treatment is binary, each person has potential outcomes $Y^0$, $Y^1$.

Example 1. Suppose treatment is influenza vaccine and the outcome is the time until the individual gets the flu. In this case potential outcomes are:
* $Y^1$: time until the individual would get the flu if they received the flu vaccine.
* $Y^0$: time until the individual would get the flu if they did _not_ receive the flu vaccine.

Example 2. Suppose the treatment is regional ($A = 1$) versus general ($A = 0$) anesthesia for hip fracture surgery. The outcome ($Y$) is major pulmonary complications.
* $Y^1$: 1 if major pulmonary complications, 0 otherwise, if given regional anesthesia.
* $Y^0$: 1 if major pulmonary complications, 0 otherwise, if given general anesthesia.

### Counterfactuals

_Counterfactual outcomes_ are ones that would have been observed, had the treatment been different.

If my treatment was $A = 1$, then my counterfactual outcome is $Y^0$. If my treatment was $A = 0$, then my counterfactual outcome is $Y^1$.

Example: Did influenza vaccine prevent me from getting the flu?
* What actually happened:
  * I got the vaccine and did not get sick.
  * My actual exposure was $A = 1$.
  * My observed outcome was $Y = Y^1$.
* What _would have_ happened (contrary to fact):
  * Had I not gotten the vaccine, would I have gotten sick?
  * My counterfactual exposure is $A = 0$.
  * My _counterfactual outcome_ is $Y^0$

**Before** the treatment decision is made, any outcome is a _potential outcome_: $Y^0$ and $Y^1$.
**After** the study, there is an _observed outcome_ $Y = Y^A$, and _counterfactual outcomes_ $Y^{1 - A}$.

Counterfactual outcomes $Y^0$, $Y^1$ are typically assumed to be the same as potential outcomes $Y^0$, $Y^1$.


## Hypothetical interventions

### Intervention

It is cleanest to think of causal effects of _interventions_ or _actions_, in other words, causal effects of variables that can be _manipulated_. Holland (1986): "No causation without manipulation." Causal effects of (hypothetical) interventions are generally well defined.

Example: Outcome if prescribed drug A vs. outcome if prescribed drug B.

### One version of treatment

It is common to assume there are <u>no hidden versions</u> of treatment.

Example: If we were interested in the causal effect of body mass index (BMI) on health outcomes, we have a problem because:
* There are many potential ways in which one could achieve a BMI of a particular value. These different ways might also be associated with different outcomes.
* Weight is not directly manipulable. It is better to think of causal effects of interventions that aim at manipulating weight.

### Immutable variables

It is also less clear what a causal effect of an immutable variable would mean.

Example: Causal effect of race, gender, or age?

When we think of potential outcome $Y^a$, we imagine that we could, hypothetically, set treatment to $A = a$ and then see an outcome. With immutable variables this is not as well defined.

### Manipulable vs. not manipulable

| No direct intervention | Manipulable with intervention |
|-|-|
| Race | Name on resume |
| Obesity | Bariatric surgery |
| Socioeconomic status | Gift of money |

### Causal effects

For the remainder of the course, we will primarily focus on treatments/exposures that could be thought of as interventions.
* Treatments that we can _imagine_ being randomized (manipulated) in a hypothetical trial (even if they may be difficult to perform due to ethical reasons, etc. in reality).

Note: There are, of course, causal effects of variables like age, race, gender, and obesity, but they do not fit as cleanly in the potential outcomes framework.

We focus on causal effects of hypothetical interventions because:
* Their meaning is well-defined.
* They are potentially actionable.

In general, $A$ had a _causal effect_ on $Y$ if $Y^1$ differs from $Y^0$.

Example:
* $Y$: headache gone one hour from now (Yes = 1, No = 0).
* $A$: take ibuprofen ($A = 1$) or not ($A = 0$).

**Not** a proper causal reasoning: "I took ibuprofen and my headache is gone, therefore the medicine worked." This statement is equivalent to $Y^1 = 1$. What _would have_ happened had you _not_ taken ibuprofen, i.e. what is $Y^0$?

There is only a causal effect if $Y^1 \neq Y^0$.

### Fundamental problem of causal inference

The _fundamental problem of causal inference_ is that we can only observe one potential outcome for each person. However, with certain assumptions, we can estimate population level (average) causal effects.

* **Hopeless**: What would have happened to me had I not taken ibuprofen? (_unit level_ or _individual level_ causal effect)
* **Possible**: What would the rate of headache remission be if everyone took ibuprofen when they had a headache vs. if no one did? (_population level_ causal effect)


## Causal effects

### Average causal effect

Given a population of interest, consider the following hypothetical worlds:
* World 1: Everyone gets $A = 0$ $\implies$ compute mean($Y$).
* World 2: Everyone gets $A = 1$ $\implies$ compute mean($Y$).

_Average causal effect_ is the difference between mean($Y$) in World 1 and mean($Y$) in World 2. Formally we can write this as:

$$\mathbb{E}(Y^1 - Y^0)$$

* Average value of $Y$ if everyone was treated with $A = 1$ minus the average value of $Y$ if everyone was treated with $A = 0$.
* If $Y$ is binary, this is a risk difference.

Example 1: Regional ($A = 1$) versus general ($A = 0$) anesthesia for hip fracture surgery on risk of major pulmonary complications.
* Suppose $\mathbb{E}(Y^1 - Y^0) = -0.1$
  * Probability of major pulmonary complications is lower by 0.1 if given regional anesthesia compared with general anesthesia.
  * If 1000 people were going to have hip fracture surgery, we would expect 100 fewer people to have pulmonary complications under regional anesthesia compared with general anesthesia.

Example 2: Treatment is thiazide diuretic ($A = 1$) or no treatment ($A = 0$) among hypertensive patients. Outcome $Y$ is systolic blood pressure.
* Suppose $\mathbb{E}(Y^1 - Y^0) = -20$ mmHg.
  * If the population of hypertensive patients took thiazide diuretics, their average systolic blood pressure would be 20 mmHg lower than if they did not take anti-hypertensive medication.

### Conditioning on vs. setting treatment

In general,

$$\mathbb{E}(Y^1 - Y^0) \neq \mathbb{E}(Y \vert A = 1) - \mathbb{E}(Y \vert A = 0)$$

Why? $\mathbb{E}(Y \vert A = 1)$ is the expected value of $Y$ given $A = 1$. This is restricting to the **subpopulation** of people who actually had $A = 1$. They might differ from the whole population in important ways. For example, people at higher risk for flu might be more likely to choose to get a flu shot.

* $\mathbb{E}(Y \vert A = 1)$: Mean of $Y$ among people with $A = 1$.
* $\mathbb{E}(Y^1)$: Mean of $Y$ if the _whole_ population was treated with $A = 1$.

$\mathbb{E}(Y \vert A = 1) - \mathbb{E}(Y \vert A = 0)$ is generally _not_ a causal effect, because it is comparing _two different populations_ of people. On the other hand, $\mathbb{E}(Y^1 - Y^0)$ is a causal effect, because it is comparing what would happen if the _same_ people were treated with $A = 1$ vs. if the _same_ people were treated with $A = 0$.

### Other causal effects

We might be interested in other causal effects, depending on the particulars of our study, our research question, or even the data available to us. Some examples:

* $\mathbb{E}(Y^1 / Y^0)$: _Causal relative risk_.
* $\mathbb{E}(Y^1 - Y^0 \vert A = 1)$: _Causal effect of treatment on the treated_. We may be interested in how well treatment works among treated people.
* $\mathbb{E}(Y^1 - Y^0 \vert V = v)$: Average causal effect in the subpopulation with covariate $V = v$.

### Challenge

We only observe one treatment and one outcome for each person (fundamental problem of causal inference).
* How do we use observed data to link observed outcomes to potential outcomes?
* What assumptions are necessary to estimate causal effects from observed data?


## Causal assumptions

### Identifiability

Identifiability of causal effects requires making some untestable assumptions. These are generally called _causal assumptions_. The most common are:
* Stable Unit Treatment Value Assumption (SUTVA)
* Consistency
* Ignorability
* Positivity

Assumptions will be about the observed data: $Y$, $A$, and a set of pre-treatment covariates $X$.

### SUTVA

The Stable Unit Treatment Value Assumption (SUTVA) really involves two assumptions.
* No _interference_.
  * Units do not interfere with each other, i.e. treatment assignment of one unit does not affect the outcome of another unit.
  * Interference is also called _spillover_ or _contagion_.
* One version of treatment.

SUTVA allows us to write potential outcome for the $i$-th person in terms of only that person's treatments.

### Consistency

The _consistency_ assumption: The potential outcome $Y^a$ under treatment $A = a$ is equal to the observed outcome if the actual treatment received is $A = a$. In other words,

$$Y = Y^a \text{ if } A = a, \text{ for all } a.$$

### Ignorability

The _ignorability_ assumption, also called "_no unmeasured confounders_" assumption: Given pre-treatment covariates $X$, treatment assignment is independent from the potential outcomes. Formally,

$$Y^0, Y^1 \indep A ~\vert~ X$$

Among people with the same values of $X$, we can think of treatment $A$ as being "randomly" assigned. Here, "random" just means being independent of the potential outcomes.

Toy example:
* $X$ is a single variable (age) that can take values "younger" or "older".
* Older people are more likely to get treatment $A = 1$.
* Older people are also more likely to have the outcome (hip fracture), regardless of treatment.

Here, $Y^0$ and $Y^1$ are not (marginally) independent from $A$. However, _within levels of $X$_, treatment might be randomly assigned, in which case the ignorability assumption would hold.

### Positivity

The _positivity_ assumption:

$$P(A = a \vert X = x) > 0 \text{ for all } a \text{ and } x.$$

In the binary treatment setting, this essentially states that, for every set of values for $X$, treatment assignment was not deterministic. If, for some values of $X$, treatment was deterministic, then we would have no observed values of $Y$ for one of the treatment groups for those values of $X$.

Variability in treatment assignment is important for identification.

### Observed data and potential outcomes

We can put these assumptions together to identify causal effects from observed data. Note that $\mathbb{E}(Y \vert A = a, X = x)$ involves only observed data.

$$
\begin{aligned}
\mathbb{E}(Y \vert A = a, X = x) &= \mathbb{E}(Y^a \vert A = a, X = x) \text{ by consistency.} \\
&= \mathbb{E}(Y^a | X = x) \text { by ignorability.}
\end{aligned}
$$

If we want a marginal causal effect, we can compute $\mathbb{E}(Y^a)$ by averaging over X.


## Stratification / standardization

### Conditioning and marginalizing

Previously we saw that under certain causal assumptions,

$$\mathbb{E}(Y \vert A = a, X = x) = \mathbb{E}(Y^a | X = x).$$

If we want marginal causal effect, we can average over the distribution of $X$. For example, if $X$ is a single categorical variable, then

$$\mathbb{E}(Y^a) = \sum_{x} \mathbb{E}(Y \vert A = a, X = x) P(X = x).$$

This is known as _standardization_.

### Standardization

Standardization involves stratifying and then averaging. We can obtain a treatment effect within each stratum and then pool across stratum, weighting by the probability (size) of each stratum. From data, you could _estimate_ a treatment effect by computing means under each treatment within each stratum, and then pooling across stratum.

### Standardization Example

Consider a study comparing two diabetes treatments, where we have new initiators of _saxagliptin_ vs. _sitagliptin_.

* Outcome: Major Adverse Cardiac Event (MACE).
* Challenge:
  * Saxagliptin users were more likely to have had past use of some other oral antidiabetic (OAD) drug.
  * Patients with past use of OAD drugs are at higher risk for MACE.
* Main idea:
  * Compute rate of MACE for saxagliptin and sitagliptin initiators in two subpopulations:
    * patients who have had no prior OAC use.
    * patients who have had prior OAC use.
  * Then take weighted average, where weights are based on proportion of people in each subpopulation.
  * This is a causal effect if, within levels of the prior OAD use variable, treatment can be thought of as randomized (i.e. ignorability given prior OAD use).

Raw data (unstratified):

| | MACE=yes | MACE=no | Total |
|-|-|-|-|
| Saxa=yes | 350 | 3650 | 4000 |
| Saxa=no | 500 | 6500 | 7000 |
| Total | 750 | 10250 | 11000 |

* $P(\text{MACE} \vert \text{Saxa=yes}) = 350/4000 = 0.088$
* $P(\text{MACE} \vert \text{Saxa=no}) = 500/7000 = 0.071$

But this does not say anything about causal effect. When we stratify the data by the prior OAD use, we have the following tables.

1. **Prior OAD use = no**:

    | | MACE=yes | MACE=no | Total |
    |-|-|-|-|
    | Saxa=yes | 50 | 950 | 1000 |
    | Saxa=no | 200 | 3800 | 4000 |
    | Total | 250 | 4750 | 5000 |

2. **Prior OAD use = yes**:

    | | MACE=yes | MACE=no | Total |
    |-|-|-|-|
    | Saxa=yes | 300 | 2700 | 3000 |
    | Saxa=no | 300 | 2700 | 3000 |
    | Total | 600 | 5400 | 6000 |

Observe that saxa users more likely to have prior OAD use. Also people with prior OAD use are at higher risk for MACE (regardless of treatment).

* For **Prior OAD use = no**:
  * $P(\text{MACE} \vert \text{Saxa=yes}) = 50/1000 = 0.05$
  * $P(\text{MACE} \vert \text{Saxa=no}) = 200/4000 = 0.05$
* For **Prior OAD use = yes**:
  * $P(\text{MACE} \vert \text{Saxa=yes}) = 300/3000 = 0.10$
  * $P(\text{MACE} \vert \text{Saxa=no}) = 300/3000 = 0.10$

Here we observe no difference in terms of treatment effectiveness of two drugs.

Mean potential outcome for saxagliptin is:

$$
\begin{aligned}
\mathbb{E}(Y^{\text{saxa}}) &= \frac{300}{3000} \cdot \frac{6000}{11000} + \frac{50}{1000} \cdot \frac{5000}{11000} \\
&= 0.077
\end{aligned}
$$

Mean potential outcome for sitagliptin is:

$$
\begin{aligned}
\mathbb{E}(Y^{\text{sita}}) &= \frac{300}{3000} \cdot \frac{6000}{11000} + \frac{200}{4000} \cdot \frac{5000}{11000} \\
&= 0.077
\end{aligned}
$$

Hence, the average causal effect is zero.

### Problem with standardization

Typically, there will be many $X$ variables needed to achieve ignorability. In this case stratification would lead to many empty cells. For example, if you stratify on age and blood pressure, there will be many combinations of age and blood pressure for which you have no data. Thus we need alternatives to standardization.

In the coming weeks, we will explore several popular methods for estimating causal effects: _matching_, _inverse probability of treatment weighting_, _propensity score methods_, etc.


# Week 2

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


# Week 3

## Observational studies

### Set up

Consider the following simple DAG: $X \rightarrow A \rightarrow Y \leftarrow X$. In this case, $X$ is sufficient to control for confounding. In other words, the ignorability assumption holds: $Y^0, Y^1 \indep A ~\vert~ X$.

### Randomized trials

In a randomized trial, treatment assignment $A$ would be determined by a coin toss. This effectively erases the arrow from $X$ to $A$.
* Observational: $X \rightarrow A \rightarrow Y \leftarrow X$.
* Randomization: $A \rightarrow Y \leftarrow X$. (No backdoor path from $A$ to $Y$.)

In a randomized trial, the distribution of $X$ will be the same in both treatment groups. In other words: \\
(Marginal distribution of $X$) \\
= (Distribution of X given $A=0$) \\
= (Distribution of X given $A=1$).

Distribution of pre-treatment variables $X$ that affect $Y$ are the same in both treatment groups. This is called **covariate balance**. Thus, if the outcome distribution ends up differing, it will not be because of differences in $X$. Here $X$ is dealt with at the experiment _design phase_.

### Why not always randomize?

* Randomized trials are expensive.
* Sometimes randomizing treatment/exposure is unethical (e.g. forcing people to smoke).
* Some/many people will refuse to participate in trials.
* Randomized trials take time (because you have to wait for outcome data). In some cases by the time you have outcome data, the question might no longer be relevant.

### Observational studies

Two types of observational studies:
1. Planned, prospective, observational studies with _active_ data collection.
   * Like trials: data collected on a common set of variables at planned times; outcomes carefully measured; study protocols.
   * Unlike trials: regulations much weaker, since not intervening; broader population eligible for the study.
2. Databases, retrospective, _passive_ data collection ("passive" means the research investigators aren't actively collecting data; data are already being collected for some other reason):
   * e.g. electronic medical records, claims, registries.
   * Large sample sizes; inexpensive; potential for rapid analysis.
   * Data quality typically lower. No uniform standard of collection.

In an observational studies, the distribution of $X$ will differ between treatment groups. For example, older people may be more likely to get $A=1$.

### Matching

**Matching** is a method that attempts to make an observational study more like a randomized trial. The main idea is to match individuals in the treatment group ($A = 1$) to individuals in the control group ($A = 0$) on the covariates $X$.

For example, let's think about the case where older people are more likely to get $A = 1$. In a randomized trial, for any particular age, there should be about the same number of treated and untreated people. By matching treated people to control people of the same age, there will be about the same number of treated and controls at any age.

### Advantages of matching

* Controlling for confounders is achieved at the design phase (i.e. without looking at the outcome). The difficult statistical work can be done completely <u>blinded to the outcomes</u>.
* Matching will reveal lack of overlap in covariate distribution. Specifically, <u>positivity assumption</u> will hold in the population that can be matched.
* Once the data are matched, we can essentially treat the study as if from a randomized trial.
* Outcome analysis can be simple.


## Overview of matching

### Single covariate

Suppose hypertensive patients are more likely to be treated than patients without hypertension, and that hypertension is the only covariate. In this case, we can match each hypertensive patient in the treated group with a hypertensive patient in the control group, and the same for non-hypertensive patients.

### Many covariates

When we have many covariates (some of them may be continuous), we probably will not be able to exactly match on the full set of covariates.

In a randomized trial, treated and control subjects are not perfect matches either. Instead, the _distribution_ of covariates is balanced between groups (**stochastic balance**).

Similarly in observational studies, matching closely on covariates can achieve stochastic balance. For example, suppose we have two covariates we would like to control for: sex (M/F) and age. For each individual in the treated group, we can try to find an individual in the control groups whose sex and age are close to the one in the treated group.

### Target population

Notice that in the examples above, we are making the distribution of covariates in the control population look like that in the _treated_ population. So what we will ultimately estimate if we follow this procedure is a **causal effect of treatment on the treated**.

Often in matching we focus on the causal effect of treatment on the treated, if you start with the treatment group and then find an individual in the control group who matches as above. You can do matching where you try to make the treated and control populations not only look like each other, but look like the population as whole, with slightly more complicated techniques. These matching methods that can be used to target a different population will not be discussed in this section.

### Fine balance

Sometimes it is difficult to find great matches. We might be willing to accept some non-ideal matches if treated and control groups have same (marginal) distribution of covariates. This is called **fine balance**.

Example.
* Match 1: {treated, male, age 40} and {control, female, age 45}.
* Match 2: {treated, female, age 45} and {control, male, age 40}.

In this case the average age and the percentage of female are the same in both groups, even though neither match is great. So we achieved fine balance here.

There is a lot of software that can impose fine balance constraints on matches.

### Number of matches

* **One to one** (pair matching): Match exactly one control to every treated subject.
* **Many to one**: Match some fixed some number $K$ controls to every treated subject (e.g. 5 to 1 matching).
* **Variable**: Sometimes match 1, sometimes more than 1, control to treated subjects.
  * Example: If multiple good matches available, use them. If not, do not.

In general one-to-one matching should result in better matching, but you are discarding some data so you may lose efficiency.


## Matching directly on confounders

### How to match?

Since we typically cannot match exactly, we first need to choose some metric of closeness. We will consider two options (for now):

* _Mahalanobis distance_ (or _M distance_)
* _Robust Mahalanobis distance_ (or _robust M distance_) ([Rosenbaum 2009](https://doi.org/10.1007/978-1-4419-1213-8))

### Mahalanobis distance

Denote by $X_j$ a vector of covariates for subject $j$. The _Mahalanobis distance_ between covariates for subject $i$ and subject $j$ is:

$$D(X_i, X_j) = \sqrt{(X_i - X_j)^\top S^{-1} (X_i - X_j)}$$

where $S = \text{cov}(X)$ is the covariance matrix and $X$ is the random vector of covariates. We can roughly think of $S^{-1}$ as a scaling factor where we scale by the unit of measure of each covariate.

This could be thought of as the square root of the sum of squared distances between each covariate scaled by the covariance matrix. We need to scale because what "big difference" means is relative.

Example. Suppose we have three covariates: age, COPD (1=yes, 0=no), sex (1=female, 0=male), and we are trying to match a treated subject with age=78.17, COPD=0, and sex=1.

| Age | COPD | Sex | **Distance** |
|-|-|-|-|
| 70.25 | 1 | 0 | 4.23 |
| 75.33 | 0 | 1 | **0.17** |
| 86.08 | 1 | 1 | 3.72 |
| 54.97 | 0 | 0 | 2.45 |
| 43.63 | 0 | 0 | 2.89 |
| 18.04 | 0 | 1 | 3.60 |

In this case we would match the treated subject with the second control subject in the table with the lowest distance.

### Robust Mahalanobis distance

Motivation: **Outliers** can create large distances between subjects, even if the covariates are otherwise similar. Hence, the **ranks** might be more relevant than the raw values of the covariates. For example the highest and the second hightest ranked values of covariates perhaps should be treated as similar, even if the values are far apart.

_Robust Mahalanobis distance_:
* Replace each covariate value with its rank.
* Use constant diagonal on covariance matrix (ranks should all be on the same scale).
* Calculate the usual Mahalanobis distance on the ranks.

### Other distance measures

If you want an exact match on a few important covariates, you can essentially make the distance infinity if they are not equal.

We can also compute distance on _propensity score_ (will be discussed later).

### What next?

Once you have a distance score, how to select matches?

* _Greedy_ (or _nearest neighbor_) matching: Not optimal, but computationally fast.
* _Optimal_ matching: Better, but computationally demanding.


## Greedy (nearest-neighbor) matching

### Set-up
Assume that:
* You have selected a set of pre-treatment covariates $X$ that (hopefully) satisfy the **ignorability** assumption.
* You have calculated a **distance** $d_{ij}$ between each treated subject with every control subject.
* You have many **more controls** than treated subjects (this is often the case).
* We are going to focus on **pair (one-to-one) matching** for now.

### Greedy matching

Steps:
1. Randomly order list of treated subjects and control subjects.
2. Start with the first treated subject. Match to the control with the smallest distance (this is "greedy").
3. Remove the matched control from the list of available matches.
4. Move on to the next treated subject. Match to the control with the smallest distance.
5. Repeat steps 3 and 4 until you have matched all treated subjects.

Greedy matching is:
* Intuitive.
* Computationally fast. Involves a series of simple algorithms (identifying minimum distance), fast even for large data sets. R package: _MatchIt_
* Not invariant to initial order of list.
* Not optimal. Always taking the smallest distance match does not minimize the total distance. Can lead to some bad matches.

### Many-to-one matching

For k:1 matching,
* After everyone has 1 match, go through the list again and find 2nd matches. Repeat until k matches.

Tradeoffs:
* Pair matching:
  * Closer matches
  * Faster computing time
* Many-to-one:
  * Larger sample size.
* Largely a bias-variance tradeoff issue.

### Caliper

We might prefer to exclude treated subjects for whom there does not exist a good match. A bad match can be defined using a **caliper** - maximum acceptable distance.
* Only match a treated subject if the best match has distance less than the caliper.
* Recall _positivity assumption_: probability of each treatment given $X$ should be non-zero.
  * If no matches within caliper, it is a sign that positivity assumption would be violated.
  * Excluding these subjects makes assumption more realistic.
  * Drawback: population is harder to define ("_all treated subjects except for those for whom there is no match available_"?).


## Optimal matching

### Greedy matching is not optimal

Greedy matching does not lead to the smallest total distance.

Example. Suppose we want to match on age. We have 3 treated subjects and 12 control subjects.
* Age (treated): 45, 38, 41.
* Age (control): 72, 47, 44, 54, 60, 36, 63, 71, 35, 56, 65, 27.

Greedy matches:

| Treated | Control |
|-|-|
| 45 | 44 |
| 38 | 36 |
| 41 | 47 |

Total distance: $1 + 2 + 6 = 9$.

Better matches:

| Treated | Control |
|-|-|
| 45 | 47 |
| 38 | 36 |
| 41 | 44 |

Total distance: $2 + 2 + 3 = 7$.

### Optimal matching

Optimal matching minimizes _global_ distance measure, e.g. total distance. This is computationally demanding: **Network flow optimization problem** ([Rosenbaum 1989](https://doi.org/10.2307/2290079); [Hansen & Klopfer 2006](https://doi.org/10.1198/106186006X137047))

R packages: _optmatch_, _rcbalance_.

### Computational feasibility

Feasibility to perform optimal matching depends on the size of the problem. Size is primarily measured by the number of possible **treatment-control pairings**.

For example, 100 treated subject and 1000 controls results in 100,000 possible pairings.
* 1 million treatment-control pairings is feasible on most computers.  (Do not expect an answer quickly, however.)
* 1 billion pairings (e.g. 10,000 treated patients and 100,000 control subjects) likely is not.

### Sparse optimal matching

Constraints can be imposed to make optimal matching computationally feasible for larger data sets. For example:
* Match within hospitals in a multi-site clinical study.
* Match within primary disease category.
* These are "blocks".

This is known as sparse matching ([Pimentel et al. 2015](https://doi.org/10.1080/01621459.2014.997879)). This can be done with _rcbalance_ R package for example. Mismatches can be tolerated if fine balance can still be achieved.


## Assessing balance

### Did matching work?

After you have matched, you should assess whether matching seemed to work.
* Covariate balance - "standardized differences". Similar means?
* This can/should be done without looking at the outcome.

Commonly, a "Table 1" is created, where pre-matching and post-matching balance is compared.

### Hypothesis tests and p-values

One could assess balance with hypothesis tests, i.e. test for a difference in means between treated and controls for each covariate:
* **Two sample t-test** for continuous covariates
* **Chi-square test** for discrete covariates

and report $p$-value for each test.

Drawbacks:
* $p$-values are dependent on **sample size**.
* Small differences in means will have a small $p$-value if the sample size is large. We probably do not care much if mean differences are small.

### Standardized differences

A **standardized (mean) difference** is the difference in means between groups, divided by the (pooled) standard deviation.

$$SMD = \frac{\overline{X}_{treatment} - \overline{X}_{control}}{\sqrt{\frac{s^2_{treatment} + s^2_{control}}{2}}}$$

Note:
* SMD does not depend on sample size.
* Often, absolute absolute value of smd is reported.
* Calculate for each variable that you match on.
* Rules of thumb:
  * Values <0.1 indicate adequate balance.
  * Values 0.1-0.2 are not too alarming.
  * Values >0.2 indicate serious imbalance.

### "Table 1"

Example. Right heart catheterization (RHC) data.

**Unmatched**:

|  | No RHC | RHC | SMD |
| n | 3551 | 2184 |  |
|-|-|-|-|
| age (mean (sd)) | 61.8 (17.3) | 60.8 (15.6) | 0.06 |
| sex = Male (%) | 53.9 | 58.5 | 0.09 |
| resp = Yes (%) | 41.7 | 28.9 | 0.27 |
| card = Yes (%) | 28.4 | 42.3 | 0.30 |
| neuro = Yes (%) | 16.2 | 5.4 | 0.35 |

**Matched**:

|  | No RHC | RHC | SMD |
| n | 2082 | 2082 |  |
|-|-|-|-|
| age (mean (sd)) | 61.6 (16.7) | 61.0 (15.8) | 0.039 |
| sex = Male (%) | 56.9 | 56.9 | 0.001 |
| resp = Yes (%) | 30.6 | 30.4 | 0.005 |
| card = Yes (%) | 39.3 | 39.5 | 0.004 |
| neuro = Yes (%) | 5.3 | 5.7 | 0.015 |

### SMD plot

We can plot SMDs, for example, in a parallel coordinate chart, where two lines correspond to matched vs. unmatched covariate SMDs and a standard cutoff (for example 0.1) is drawn as a straight line.


## Analyzing data after matching

### After matching

After successfully matching and achieving adequate balance, proceed with outcome analysis.
* Test for a treatment effect.
* Estimate a treatment effect and confidence interval.
* Methods should take matching into account.

### Randomization tests

**Randomization tests** are also known as **permutation tests** or **exact tests**. The main idea is:
* Compute test statistic from observed data.
* Assume null hypothesis of no treatment effect is true.
* Randomly permute treatment assignment within pairs and re-compute test statistic.
* Repeat many times and see how unusual observed statistic is.

### Randomization test example

Suppose we have a binary outcome and 13 matched pairs. We will use as the test statistic <u>the number of events in the treated group</u>.

Observed data:

| Matched pair | Treated | Control |
|-|-|-|
| 1 | 0 | 0 |
| 2 | 1 | 0 |
| 3 | 1 | 0 |
| 4 | 0 | 0 |
| 5 | 1 | 1 |
| 6 | 0 | 0 |
| 7 | 0 | 1 |
| 8 | 0 | 1 |
| 9 | 0 | 0 |
| 10 | 1 | 0 |
| 11 | 0 | 0 |
| 12 | 1 | 0 |
| 13 | 1 | 0 |

Test statistic: **6**. Would this number be unusual if null was true? We answer this question by randomly permuting the values in each row.

Randomly permuted data:

| Matched pair | Treated | Control |
|-|-|-|
| 1 | 0 | 0 |
| 2 | 1 | 0 |
| 3 | **0** | **1** |
| 4 | 0 | 0 |
| 5 | 1 | 1 |
| 6 | 0 | 0 |
| 7 | **1** | **0** |
| 8 | 0 | 1 |
| 9 | 0 | 0 |
| 10 | **0** | **1** |
| 11 | 0 | 0 |
| 12 | 1 | 0 |
| 13 | 1 | 0 |

Test statistic: **5**.

If we permute like this 1,000 times and record the test statistic each time, we will get a distribution of the test statistic. We want to know how _unusual_ our observed data value is given the distribution. To compute the $p$-value, we add up all probabilities of the test statistic values as unusual or more unusual than the observed value.

### McNemar test

This test is equivalent to the McNemar test for paired data. First we create a table of treated/control outcomes (columns: control outcomes, rows: treated outcomes):

| Outcomes | 1 | 0 |
| 1 | 1 | 5 |
| 0 | 2 | 5 |

Now we can compute the $p$-value using this R code:
```
ex <- matrix(c(1, 5, 2, 5), 2, 2)
mcnemar.test(ex)
```

$p$-value for the above example is 0.4497 (not significant). This will roughly be the same as using permutation.

### Continuous data

The basic approach also works for continuous data. Suppose you have a systolic blood pressure (SBP) outcome data from 20 treatment-control pairs. Let the test statistic be the difference in sample means.

Similarly we can randomly permute labels and re-calculate the difference in sample means of SBP, and do this for 1,000 times.

We could also use a **paired t-test** for continuous data. For example in R,
```
t.test(txsbp, consbp, paired=TRUE)
```

### Other outcome models

Some examples of other outcome models:
* **Conditional logistic regression**: Matched binary outcome data.
* **Stratified Cox model**: Time-to-event (survival) outcome data. Baseline hazard function stratified on matched sets.
* **Generalized estimating equations (GEE)**: Match ID variable used to specify clusters. For binary outcomes, can estimate a causal risk difference, causal risk ratio, or causal odds ratio, depending on link function (identity link for a causal risk difference, log link for a causal risk ratio).


## Sensitivity analysis

### Possible hidden bias

Matching aims to achieve balance on _observed_ covariates. Overt bias could occur if there was imbalance on observed covariates (i.e. we did not fully control for these variables).

However, there is no guarantee that matching will result in balance on variables that we did not match on (including unobserved variables). If these unobserved variables are confounders, then we have _hidden_ bias (i.e. ignorability assumption violated; unmeasured confounding).

### Sensitivity analysis

Main idea: If there is hidden bias, determine how severe it would have to be to **change conclusions**. For example,
* Change from statistically significant to not.
* Change in direction of effect.

Let $\pi_j$ be the probability that person $j$ receives treatment, and the same for $\pi_k$. Suppose person $j$ and $k$ are perfectly matched, so that their observed covariates, $X_j$ and $X_k$, are the same. If $\pi_j = \pi_k$, then there is no hidden bias.

Consider the following inequality:

$$\frac{1}{\Gamma} \leq \frac{\frac{\pi_j}{1 - \pi_j}}{\frac{\pi_k}{1 - \pi_k}} \leq \Gamma$$

* The middle term is the odds ratio, and we select $\Gamma \geq 1$.
* If $\Gamma = 1$, then this implies the odds ratio is $1$, no overt bias.
* $\Gamma > 1$ implies hidden bias.

Suppose we have evidence of a treatment effect. Note that this is under the assumption that $\Gamma = 1$, i.e. no hidden bias.

We can then increase $\Gamma$ until evidence of treatment effect goes away (e.g. no longer statistically significant). If, say, this happens when $\Gamma = 1.1$, then the conclusion is **very sensitive** to unmeasured confounding (hidden bias). If this does not happen until $\Gamma = 5$, then the conclusion is **not very sensitive** to hidden bias.

The actual methods to perform these analyses are beyond the scope of this course, but more details can be found at [Rosenbaum 2009](https://doi.org/10.1007/978-1-4419-1213-8). R packages: _sensitivity2x2xk_, _sensitivityfull_.


## Data example in R

Right heart catheterization data, publicly available [here](https://hbiostat.org/data/) (Note: The link included in the lecture video is outdated). ICU patients in 5 hospitals.

* **Treatment**: right heart catheterization (rhc) vs. not
* **Outcome**: death (yes/no)
* **Confounders**: demographics, insurance, disease diagnoses, etc.
* 2184 treated and 3551 controls.

### First steps

Load packages, read in data, view data:

```R
#install packages
install.packages("tableone")
install.packages("Matching")

#load packages
library(tableone)
library(Matching)

#read in data
load(url("https://hbiostat.org/data/repo/rhc.sav"))
#view data
View(rhc)
```

Optionally we can create a new data set: only variables that will be used, convert character to numeric.

```R
#create a data set with just these variables, for simplicity
ARF<-as.numeric(rhc$cat1=='ARF')
CHF<-as.numeric(rhc$cat1=='CHF')
Cirr<-as.numeric(rhc$cat1=='Cirrhosis')
colcan<-as.numeric(rhc$cat1=='Colon Cancer')
Coma<-as.numeric(rhc$cat1=='Coma')
COPD<-as.numeric(rhc$cat1=='COPD')
lungcan<-as.numeric(rhc$cat1=='Lung Cancer')
MOSF<-as.numeric(rhc$cat1=='MOSF w/Malignancy')
sepsis<-as.numeric(rhc$cat1=='MOSF w/Sepsis')
female<-as.numeric(rhc$sex=='Female')
died<-as.numeric(rhc$death=='Yes')
age<-rhc$age
treatment<-as.numeric(rhc$swang1=='RHC')
meanbp1<-rhc$meanbp1
aps<-rhc$aps1

#new dataset
mydata<-cbind(ARF,CHF,Cirr,colcan,Coma,lungcan,MOSF,sepsis,
              age,female,meanbp1,treatment,died)
mydata<-data.frame(mydata)

#covariates we will use (shorter list than you would use in practice)
xvars<-c("ARF","CHF","Cirr","colcan","Coma","lungcan","MOSF","sepsis",
         "age","female","meanbp1")
```

### Create a "Table 1", pre-matching

```R
#look at a table 1
table1<- CreateTableOne(vars=xvars,strata="treatment", data=mydata, test=FALSE)
## include standardized mean difference (SMD)
print(table1,smd=TRUE)
```

Results:
```
                     Stratified by treatment
                      0             1             SMD   
  n                    3551          2184               
  ARF (mean (SD))      0.45 (0.50)   0.42 (0.49)   0.059
  CHF (mean (SD))      0.07 (0.25)   0.10 (0.29)   0.095
  Cirr (mean (SD))     0.05 (0.22)   0.02 (0.15)   0.145
  colcan (mean (SD))   0.00 (0.04)   0.00 (0.02)   0.038
  Coma (mean (SD))     0.10 (0.29)   0.04 (0.20)   0.207
  lungcan (mean (SD))  0.01 (0.10)   0.00 (0.05)   0.095
  MOSF (mean (SD))     0.07 (0.25)   0.07 (0.26)   0.018
  sepsis (mean (SD))   0.15 (0.36)   0.32 (0.47)   0.415
  age (mean (SD))     61.76 (17.29) 60.75 (15.63)  0.061
  female (mean (SD))   0.46 (0.50)   0.41 (0.49)   0.093
  meanbp1 (mean (SD)) 84.87 (38.87) 68.20 (34.24)  0.455
```

### Match

Perform greedy matching and create "Table 1" on matched data:

```R
#do greedy matching on Mahalanobis distance
greedymatch<-Match(Tr=treatment,M=1,X=mydata[xvars],replace=FALSE)
matched<-mydata[unlist(greedymatch[c("index.treated","index.control")]), ]

#get table 1 for matched data with standardized differences
matchedtab1<-CreateTableOne(vars=xvars, strata ="treatment", 
                            data=matched, test = FALSE)
print(matchedtab1, smd = TRUE)
```

Results:
```
                     Stratified by treatment
                      0             1             SMD   
  n                    2184          2184               
  ARF (mean (SD))      0.42 (0.49)   0.42 (0.49)   0.006
  CHF (mean (SD))      0.10 (0.29)   0.10 (0.29)  <0.001
  Cirr (mean (SD))     0.02 (0.15)   0.02 (0.15)  <0.001
  colcan (mean (SD))   0.00 (0.02)   0.00 (0.02)  <0.001
  Coma (mean (SD))     0.04 (0.20)   0.04 (0.20)  <0.001
  lungcan (mean (SD))  0.00 (0.05)   0.00 (0.05)  <0.001
  MOSF (mean (SD))     0.07 (0.26)   0.07 (0.26)  <0.001
  sepsis (mean (SD))   0.24 (0.43)   0.32 (0.47)   0.177
  age (mean (SD))     61.53 (16.15) 60.75 (15.63)  0.049
  female (mean (SD))   0.44 (0.50)   0.41 (0.49)   0.042
  meanbp1 (mean (SD)) 73.12 (34.28) 68.20 (34.24)  0.144
```

### Outcome analysis

If we want a causal risk difference, we can carry out a paired t-test. Note that a paired t-test is actually just a regular t-test on the difference in the outcome among matched pairs, so we can just use the standard t-test command on the difference.

```R
#outcome analysis
y_trt<-matched$died[matched$treatment==1]
y_con<-matched$died[matched$treatment==0]

#pairwise difference
diffy<-y_trt-y_con

#paired t-test
t.test(diffy)
```

Results:
```
	One Sample t-test

data:  diffy
t = 3.9289, df = 2183, p-value = 8.799e-05
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 0.02706131 0.08099730
sample estimates:
mean of x 
0.0540293 
```

### Causal risk difference

* Point estimate: 0.054 (The course video shows 0.045). Difference in probability of death if everyone received RHC vs. if no one received RHC is 0.054, i.e. higher risk of death in RHC group.
* 95% CI: (0.027, 0.081) (The course video shows (0.019, 0.072)).
* p-value: $<0.001$

### McNemar test

```R
#McNemar test
table(y_trt,y_con)
```

Results:
```
     y_con
y_trt   0   1
    0 303 395
    1 513 973
```

* 513+395 discordant pairs.
* The 513 is when a treated person died and a control person did not.

```R
mcnemar.test(matrix(c(973,513,395,303),2,2))
```

Results:
```
	McNemar's Chi-squared test with continuity correction

data:  matrix(c(973, 513, 395, 303), 2, 2)
McNemar's chi-squared = 15.076, df = 1, p-value = 0.0001033
```

* p-value about the same as from paired t-test ($<0.001$).

### Discussion

* If we wanted a causal risk ratio or causal odds ratio, could use GEE with log or logit link, respectively.
* We only used a subset of covariates for simplicity. In practice we would want to include more variables.
* _rcbalance_ package has many more matching options: [_rcbalance_ paper](https://obsstudies.org/wp-content/uploads/2017/06/rcbalance_paper_v7r2.pdf)
* The _tableone_ package vignette has useful code for creating figures and tables: [link](https://cran.r-project.org/web/packages/tableone/vignettes/smd.html)


## Propensity scores

### Propensity score

The _propensity score_ is the probability of receiving treatment (rather than control), given covariates $X$. Propensity score $\pi_i$ for a subject $i$ is defined by:

$$\pi_i := P(A = 1 \vert X_i)$$

Suppose age was the only $X$ variable, and older people were more likely to get treatment. This implies the propensity score would be larger for older ages, e.g. $P(A = 1 \vert \text{age} = 60) > P(A = 1 \vert \text{age} = 30)$. More generally,

$$\pi_i > \pi_j \text{ if } \text{age}_i > \text{age}_j$$

If a person $i$ has a propensity score value of 0.3, that means that, given their particular covariate values, there is a 30% chance they will be treated.

### Balancing score

Suppose two subjects have the same value of the propensity score, but they possibly have different covairate values $X$. Despite the different covariate values, they were both equally likely to have been treated. This means both subjects' $X$ is just as likely to be found in the treatment group.

Propensity score is a _balancing score_, i.e. if you restrict to a subpopulation of subjects who have the same value of the propensity score, there should be balance in the two treatment groups. More formally,

$$P(X=x \vert \pi(X)=p, A=1) = P(X=x \vert \pi(X)=p, A=0).$$

This can be proved using Bayes' theorem. <u>This implies that if we match on the propensity score, we should achieve balance</u>. This makes sense, considering we assumed ignorability - that treatment is randomized given $X$. Note that conditioning on the propensity score is conditioning on an **allocation probability**.

### Estimated propensity score

In a randomized trial, the propensity score is generally known, e.g. $P(A = 1 \vert X) = P(A = 1) = 0.5$.

In an observational study, it will be unknown, but we can estimate it given the observed data (since the propensity score involves only observed data $A$ and $X$). Typically when people talk about a propensity score, they are referring to the **estimated** propensity score.

We estimate the score $P(A=1 \vert X)$ by, for example, fitting a logistic regression model with covariates $X$ and outcome $A$. After fitting the model, the estimated propensity score is the predicted probability (fitted value) for each subject.


## Propensity score matching

### Matching

Propensity score is a balancing score, so matching on the propensity score (a scalar value for each subject) should achieve balance. This greatly simplifies matching, since we are only matching on one variable.

### Overlap

Once the propensity score is estimated, but before matching, it is useful to look for overlap. We compare the distribution of the propensity score for treated and control subjects in a plot.

Accross different values of the propensity score we make sure that there is good overlap between control and treated groups. This implies that positivity assumption is reasonable.

If there is poor overlap, we can only hope to learn about causal effects within the ranges of propensity scores where there is good overlap. This is called "trimming the tails", meaning removing subjects who have extreme values of the propensity score. For example we can remove:

* Control subjects whose propensity score is less than the minimum in the treatment group.
* Treated subjects whose propensity score is greater than the maximum in the control group.

Trimming the tails makes the positivity assumption more reasonable (this prevents extrapolation).

### Back to matching

We can now proceed by computing a distance between the propensity score for each treated subject with every control. Then we can use nearest neighbor or optimal matching, as before.

In practice, **logit** (log-odds) of the propensity score is often used, rather than the propensity score itself (i.e. match on $\text{logit}(\pi)$ rather than $\pi$). The propensity score is bounded between 0 andn 1, making many values seem similar, while the logit of the propensity score is unbounded. This transformation essentially stretches the distribution, while preserving ranks.

### Caliper

To ensure that we do not accept any bad matches, a caliper (maximum distance that we are willing to tolerate) can be used. In practice, a common shoice for a cliper is the 0.2 times the standard deviation of logit of the propensity score. In detail:

1. Estimate the propensity score (e.g. using logistic regression).
2. Logit-transform the propensity score.
3. Take the st. dev. of this transformed variable.
4. Set the caliper to 0.2 times the value from Step 3.

This is commonly done in practice because it seems to work well, but it is somewhat arbitraty. Smaller caliper implies less bias, more variance.

### After matching

The outcome analysis methods can be the same as would be used if matching directly on covariates, e.g. randomization tests, conditional logistic regression, GEE, stratified Cox model, etc.


## Propensity score matching in R

### Data example

We use the same right heart catheterization data discussed above.

### Fit propensity score model

Starting from the code discussed previously, we fit a propensity score model (logistic regression):

```R
#fit a propensity score model. logistic regression

psmodel<-glm(treatment~ARF+CHF+Cirr+colcan+Coma+lungcan+MOSF+
               sepsis+age+female+meanbp1+aps,
    family=binomial(),data=mydata)

#show coefficients etc
summary(psmodel)
#create propensity score
pscore<-psmodel$fitted.values
```

Output:

```
Coefficients:
              Estimate Std. Error z value Pr(>|z|)    
(Intercept) -1.9460154  0.2321291  -8.383  < 2e-16 ***
ARF          1.2252930  0.1495511   8.193 2.54e-16 ***
CHF          1.8905642  0.1735687  10.892  < 2e-16 ***
Cirr         0.4334062  0.2203366   1.967  0.04918 *  
colcan       0.0481566  1.1242894   0.043  0.96583    
Coma         0.6842545  0.1878333   3.643  0.00027 ***
lungcan      0.1984600  0.5055005   0.393  0.69461    
MOSF         1.0177797  0.1807159   5.632 1.78e-08 ***
sepsis       1.8402456  0.1561589  11.784  < 2e-16 ***
age         -0.0030469  0.0017462  -1.745  0.08101 .  
female      -0.1390768  0.0590139  -2.357  0.01844 *  
meanbp1     -0.0075166  0.0008707  -8.633  < 2e-16 ***
aps          0.0182356  0.0017286  10.549  < 2e-16 ***
```

### Plot propensity score

Now we plot the propensity score, pre-matching, for treated and control groups separately and check overlap (not shown here).

### Matching using "MatchIt" package

We can use `MatchIt` package directly to match (without running logistic regression manually):

```R
libary(MatchIt)

#use matchit for propensity score, nearest neighbor matching
m.out <- matchit(treatment~ARF+CHF+Cirr+colcan+Coma+lungcan+MOSF+
                 sepsis+age+female+meanbp1+aps, data=mydata, method = "nearest")
summary(m.out)

#propensity score plots
plot(m.out,type="jitter")
plot(m.out,type="hist")
```

### Matching using "Match" package

We can also match on logit(propensity score) using `Match` package, with or without caliper.

Without a caliper:

```R
#do greedy matching on logit(PS)

logit <- function(p) {log(p)-log(1-p)}
psmatch<-Match(Tr=mydata$treatment,M=1,X=logit(pscore),replace=FALSE)
matched<-mydata[unlist(psmatch[c("index.treated","index.control")]), ]
xvars<-c("ARF","CHF","Cirr","colcan","Coma","lungcan","MOSF","sepsis",
         "age","female","meanbp1")

#get standardized differences
matchedtab1<-CreateTableOne(vars=xvars, strata ="treatment", 
                            data=matched, test = FALSE)
print(matchedtab1, smd = TRUE)
```

With a caliper:

```R
#do greedy matching on logit(PS) using Match with a caliper

logit <- function(p) {log(p)-log(1-p)}
psmatch<-Match(Tr=mydata$treatment,M=1,X=logit(pscore),replace=FALSE,caliper=.2)
matched<-mydata[unlist(psmatch[c("index.treated","index.control")]), ]
xvars<-c("ARF","CHF","Cirr","colcan","Coma","lungcan","MOSF","sepsis",
         "age","female","meanbp1")

#get standardized differences
matchedtab1<-CreateTableOne(vars=xvars, strata ="treatment", 
                            data=matched, test = FALSE)
print(matchedtab1, smd = TRUE)
```

Output (with a caliper):
```
                     Stratified by treatment
                      0             1             SMD   
  n                    1900          1900               
  ARF (mean (SD))      0.47 (0.50)   0.45 (0.50)   0.036
  CHF (mean (SD))      0.10 (0.30)   0.10 (0.29)   0.005
  Cirr (mean (SD))     0.02 (0.16)   0.03 (0.16)   0.007
  colcan (mean (SD))   0.00 (0.00)   0.00 (0.02)   0.032
  Coma (mean (SD))     0.04 (0.21)   0.05 (0.21)   0.018
  lungcan (mean (SD))  0.00 (0.06)   0.00 (0.05)   0.010
  MOSF (mean (SD))     0.08 (0.28)   0.08 (0.27)   0.017
  sepsis (mean (SD))   0.26 (0.44)   0.27 (0.44)   0.030
  age (mean (SD))     60.85 (18.04) 61.08 (15.41)  0.014
  female (mean (SD))   0.44 (0.50)   0.43 (0.49)   0.018
  meanbp1 (mean (SD)) 71.87 (34.74) 71.53 (34.85)  0.010
```

### Outcome analysis

As before, we can perform outcome analysis using t-test as follow:

```R
#outcome analysis
y_trt<-matched$died[matched$treatment==1]
y_con<-matched$died[matched$treatment==0]

#pairwise difference
diffy<-y_trt-y_con

#paired t-test
t.test(diffy)
```

Output:
```
	One Sample t-test

data:  diffy
t = 2.2573, df = 1899, p-value = 0.02411
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 0.004486724 0.063934329
sample estimates:
 mean of x 
0.03421053 
```

Without caliper:
* 2184 matched pairs
* Causal risk difference: 0.04 (0.012, 0.068)

With caliper:
* 1900 matched pairs
* Causal risk difference: 0.03 (0.004, 0.063)


# Week 4

## Intuition for Inverse Probability of Treatment Weighting (IPTW)

### Motivating example

Suppose there is a single binary confounder $X$. Suppose that $P(A = 1 \vert X = 1)$, i.e. the value of the propensity score for people with $X = 1$ is 0.1 (among people with $X = 1$, only 10% will receive the treatment). Similarly suppose that $P(A = 1 \vert X = 0) = 0.8$

Imagine there are 10 individuals with $X = 1$. Given the above assumption, 9 of them would be in the control group and 1 of them in the treated group. If we do matching, we'd use 1 individual in the treated group and 1 individual in the control group (who now represent all 9 individuals in the control group). Rather than matching, we could use all of the data, but down-weight some and up-weight others.

### Weighting

This is accomplished by weighting by the inverse of the probability of treatment **received**.
* For treated subjects, weight by the inverse of $P(A = 1 \vert X)$
* For control subjects, weight by the inverse of $P(A = 0 \vert X)$

This is known as **inverse probability of treatment weighting (IPTW)**.

In the above example with $X = 1$, the person in the treated group will get a weight of 10, while the 9 people in the control group will each get a weight of 10/9.

Similarly if there are 5 people with $X = 0$ in the above example, there will be 4 people in the treatment group and 1 person in the control group. The people in the treated group will each get a weight 5/4, while the person in the control group will get a weight of 5.


## More intuition for IPTW estimation

### Motivation: survey sampling

In surveys it is common to oversample some groups relative to the population. For example,
* Oversample a minority group
* Oversample older adults
* Oversample obese individuals

To estimate the population mean, we can weight the data to account for the oversample. This kind of approach is known as **Horvitz-Thompson estimator**.

### Observational studies

In an observational study, certain groups are oversampled relative to the hypothetical sample from a randomized trial (there is a confounding in the original population).

IPTW creates a _pseudo-population_ where treatment assignment no longer depends on $X$ (no confounding in the pseudo-population).

### Pseudo-population

Example. Suppose $P(A = 1 \vert X) = 0.9$. If there are 10 people in the original population, 9 of them would be treated and 1 of them would be in control. In this case IPTW applied the weight of 1/0.9 = 10/9 weight to each in the treated group and the weight of 1/0.1 = 10 for the individual in the control group. Now in the pseudo-population (after applying weighting), there will be 10 individuals in both treated and control group.

In the original population, some people were more likely to get treated than others, based on their $X$ values. In the pseudo-population on the other hand, everyone is equally likely to be treated, regardless of their $X$ values.

### Estimator

Under the assumption of exchangeability and positiity, we can estimate $E(Y^1)$ as:

$$\frac{\sum_{i = 1}^n I(A_i = 1) \frac{Y_i}{\pi_i}}{\sum_{i = 1}^n \frac{I(A_i = 1)}{\pi_i}}$$

where $\pi_i = P(A = 1 \vert X_i)$ is the propensity score and $I$ is the indicator function. Note that the numerator is the sum of the $Y$'s in treated _pseudo_-population, and the denominator is the number of subjects in treated _pseudo_-population.


## Marginal structural models

### Motivation

Previously we discussed IPTW estimation for simple causal effects, such as an average causal effect. However, IPTW estimation methods can be used more generally to estimate causal effect parameters from models.

### Marginal structural models

A marginal structural model (MSM) is a model for the mean of the _potential outcomes_.

* **Marginal**: model that is not conditional on the confounders (population average).
* **Structural**: model for potential outcomes, not observed outcomes.

### Linear MSM

Linear MSM is as follows:

$$E(Y^a) = \psi_0 + \psi_1 a, ~ a \in \{0, 1\}$$

In other words,
* $E(Y^0) = \psi_0$
* $E(Y^1) = \psi_0 + \psi_1$

So $\psi_1$ is the average causal effect $E(Y^1) - E(Y^0)$.

### Logistic MSM

Logistic MSM for binary outcome is:

$$logit(E(Y^a)) = \psi_0 + \psi_1 a, ~ a \in \{0, 1\}$$

So $exp(\psi_1)$ is the causal odds ratio:

$$\frac{\frac{P(Y^1 = 1)}{1 - P(Y^1 = 1)}}{\frac{P(Y^0 = 1)}{1 - P(Y^0 = 1)}}$$

### MSM with effect modification

MSMs can also include effect modifiers. Suppose $V$ is a variable that modifies the effect of $A$. A linear MSMS with effect modification is:

$$E(Y^a \vert V) = \psi_0 + \psi_1 a + \psi_2 V + \psi_3 aV, ~ a \in \{0, 1\}$$

So $E(Y^1 \vert V) - E(Y^0 \vert V) = \psi_1 + \psi_3 V$.

### General MSM

$$g(E(Y^a \vert V)) = h(a, V; \psi)$$

where $g$ is a link function, $h$ is a function specifying parametric form of $a$ and $V$ (typically additive, linear).


## IPTW estimation

### Estimation in regression models

First consider estimation of parameters from a linear regression model:

$$Y = X\beta + \epsilon$$

Esimation involves solving:

$$\sum_{i = 1}^{n} X_i (Y_i - X_i^T \hat{\beta}) = 0$$

for $\hat{\beta}$. This is the value of $\beta$ that minimizes the sum of squared deviations (least squares estimator).

### Estimation in generalized linear models

Now consider estimation of parameters from a generalized linear regression model:

$$E(Y_i \vert X_i) = \mu_i = g^{-1}(X_i^T \beta)$$

Estimation involves solving:

$$\sum_{i = 1}^n \frac{\partial \mu_i^T}{\partial \beta} V_i^{-1} (Y_i - \mu_i(\beta)) = 0$$

for $\beta$.

### Estimation in MSMs

Marginal structural models look a lot like generalized linear models. For example:

$$E(Y_i^a) = g^{-1}(\psi_0 + \psi_1 a)$$

This model is **not equivalent** to the regression model

$$E(Y_i \vert A_i) = g^{-1}(\psi_0 + \psi_1 A_i)$$

because of confounding. However, recall that the **pseudo-population** (obtained from IPTW) is free from confounding (assuming ignorability and positivity). We can therefore estimate MSM parameters by solving estimating equations for the observed data of the pseudo-population:

$$\sum_{i=1}^{n} \frac{\partial \mu_i^T}{\partial \psi} V_i^{-1} W_i (Y_i - \mu_i(\psi)) = 0$$

where $W_i = \frac{1}{A_i P(A = 1 \vert X_i) + (1 - A_i) P(A = 0 \vert X_i)}$.

Steps:

1. Estimate propensity score.
2. Create weights
   * 1 divided by propensity score for treated subjects.
   * 1 divided by 1 minus the propensity score for control subjects.
3. Specify the MSM of interest.
4. Use software to fit a weighted generalized linear model.
5. Use asymptotic (sandwich) variance estimator (or bootstrapping). This accounts for fact that pseudo-population might be larger than sample size.


## Assessing balance

### Balance after weighting

Covariate balance can be checked on the weighted sample using standardized differences. We can use:
* Table 1
* Plot

### Standard differences after weighting

Recall that a standardized difference is the difference in means between groups, divided by the (pooled) standard deviation.

$$smd = \frac{\overline{X}_{treatment} - \overline{X}_{control}}{\sqrt{\frac{s^2_{treatment} + s^2_{control}}{2}}}$$

A **standard difference after weighting** is the same idea as the usual standard differences, except on weighted means and weighted variances.
* Stratify on treatment group: find weighted mean and weighted variance for each group. This can be done directly of with software tools that were developed for surveys (e.g. `svydesign` in R).
* Take difference in weighted means and divide by an estimate of the pooled (weighted) standard deviation.

One can show this in "Table 1" (raw & weighted data) or in a plot.

### If imbalance after weighting

* Can refine propensity score model. Interactions? Non-linearity?
* Can then reassess balance.


## Distribution of weights

### Why do weights matter?

Larger weights lead to noisier estimates of causal effects. Why?

Suppose 1 person has weight of 10,000. They essentially represent 10,000 people. If the outcome is binary, whether they have the event or not could have a big impact on the parameter estimate. If one person's outcome data can greatly affect the parameter estimate, then the standard error will be large.

### Further intuition: bootstrapping

One way to estimate standard errors is bootstrapping:
1. Randomly sample, with replacement, from the original sample.
2. Estimate parameters.
3. Repeat steps 1 and 2 many times.
4. The standard deviation of the bootstrap estimates is an estimate of the standard error.

Someone with a very large weight will be included in some bootstrap samples, but not others. Whether or not they are included will have a relatively large impact on the parameter estimates. Thus, a lot of the variability of the estimator would be due to this one subject.

### Relationship with positivity assumption

An extremely large weight means that the probablity of that treatment was very small. Thus, large weights indicate _near violations_ of the positivity assumption, i.e. people with certain values of the covariates are very unlikely to get one of the treatments.

### Checking weights

The easiest way to check the weights is to show the weights in a density histogram or in a plot of sorted weight values vs. index. Or one can get summary statistics of weights with `summary(weight)` command in R for example.


## Remedies for large weights

### Very large weights: investigative step

A good first step is to look into why the weights are large. After identifying the subjects who have large weights, ask:
* What is unusual about them?
* Is there a problem with their data?
* Is there a problem with the propensity score model?

### Trimming the tails

Large weights occur at observations in the tails of the propensity score distribution. Trimming the tails can eliminate some of the extreme weights. This means removing subjects who have extreme values of the propensity score. A common trimming strategy:
* Remove treated subjects whose propensity scores are above the 98th percentile from the distribution among controls.
* Remove control subjects whose propensity scores are below the 2nd percentile from the distribution of treated subjects.

<u>Reminder</u>: Trimming the tails changes the population.

### Weight truncation

Truncating the large weights is another option. Steps:
* Determine (ahead of time) a maximum allowable weight. Could be a specific value (e.g. 100) or could be based on a percentile (e.g. 99th).
* If a weight is greater than the maximum allowable, set it to the maximum allowable value. For example, if your upper limit is 100, and someone has a weight of 10,000, set their weight to 100 instead.

Whether or not to truncate weights involves a bias-variance trade-off:
* Truncation: bias, but smaller variance.
* No truncation: unbiased, larger variance.

Truncating extremely large weights can result in estimators with lower mean squared error (MSE).


## Doubly robust estimators

### Background: IPTW estimation

We can estimate $E(Y^1)$ using IPTW:

$$\frac{1}{n} \sum_{i = 1}^n \frac{A_i Y_i}{\pi_i(X_i)}$$

If the propensity score is correctly specified, this estimator is unbiased.

### Background: Regression-based estimation

Alternatively, we could estimate $E(Y^1)$ by specifying an outcome model $m_1(X) = E(Y \vert A = 1, X)$ and then averaging over the distribution of X:

$$\frac{1}{n} \sum_{i = 1}^n (A_i Y_i + (1 - A_i) m_1(X_i))$$

* The first term in the sum: For subjects with $A = 1$, use observed $Y$.
* The second term in the sum: For other subjects, use predicted value of $Y$ given their $X$'s if their $A$ had been 1.

If outcome model is correctly specified, then this estimator is unbiased.

### Doubly robust estimators

A **doubly robust estimator** (also known as augmented inverse probability of treatment weighting) is an estimator that is unbiased if _either_ the propensity score model or the outcome regresison model is correctly specified.

Example (for estimating $Y^1$):

$$\frac{1}{n} \sum_{i = 1}^n \left[\frac{A_i Y_i}{\pi_i(X_i)} - \frac{A_i - \pi_i(X_i)}{\pi_i(X_i)} m_1(X_i)\right]$$

The first term in the sum is IPTW and the second term is "augmentation".

Suppose that propensity score is correctly specified (i.e. $\pi(X) = P(A = 1 \vert X)$), but outcome model is not (i.e. $m_1(X) \neq E(Y \vert A = 1, X)$). In that case, the expectation of $A_i$ term is equal to propensity score so the second term in the term has expectation 0. Then the formula becomes a standard IPTW, so the esimator is unbiased.

Now suppose that propensity score is wrong (i.e. $\pi(X) \neq P(A = 1 \vert X)$), but outcome model is correct (i.e. $m_1(X) = E(Y \vert A = 1, X)$):

$$\begin{align}
\frac{1}{n} \sum_{i = 1}^n \left[\frac{A_i Y_i}{\pi_i(X_i)} - \frac{A_i - \pi_i(X_i)}{\pi_i(X_i)} m_1(X_i)\right] \\
= \frac{1}{n} \sum_{i = 1}^n \left[\frac{A_i (Y_i - m_1(X_i))}{\pi_i(X_i)} + m_1(X_i)\right]
\end{align}$$

In this case the first term in the sum goes to 0 and the second term goes to $E(Y^1)$.

These are also known as augmented IPTW (AIPTW) estimators. One can use semiparametric theory to identify best estimators. In general, AIPTW estimators should be **more efficient** than regular IPTW estimators.

