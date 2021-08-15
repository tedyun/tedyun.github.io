---
layout: post
title: "Causal inference course note"
math: true
published: true
last_modified_at: 2021-07-25 0:00:00 +0000
---

This is my note for the "_A Crash Course in Causality: Inferring Causal Effects from Observational Data_" course by [Jason A. Roy](https://sph.rutgers.edu/concentrations/biostatistics-epidemiology/faculty-member.php?id=97108) on [Coursera](https://www.coursera.org/learn/crash-course-in-causality).

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

TODO.


## Propensity score

The _propensity score_ is the probability of receiving treatment (rather than control), given covariates $X$. Propensity score $\pi_i$ for a subject $i$ is defined by:

$$\pi_i := P(A = 1 \vert X_i)$$

Propensity score is a _balancing score_, i.e. if you restrict to a subpopulation of subjects who have the same value of the propensity score, there should be balance in the two treatment groups. More formally,

$$P(X=x \vert \pi(X)=p, A=1) = P(X=x \vert \pi(X)=p, A=0).$$

This can be proved using Bayes' theorem. This implies that if we match on the propensity score, we should achieve balance. Note that conditioning on the propensity score is conditioning on an **allocation probability**.

In a randomized trial, the propensity score is generally known, e.g. 0.5. In an observational study, it will be unknown, but we estimate it given the observed data. We estimate the score $P(A=1 \vert X)$ by, for example, fitting a logistic regression model with covariates $X$ and outcome $A$. After fitting the model, the estimated propensity score is the predicted probability (fitted value) for each subject.
