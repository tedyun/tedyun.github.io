---
layout: post
title: "Causal inference course note - Week 3"
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

# Week 3. Matching and Propensity Scores

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
* Often, absolute value of smd is reported.
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

In practice, **logit** (log-odds) of the propensity score is often used, rather than the propensity score itself (i.e. match on $\text{logit}(\pi)$ rather than $\pi$). The propensity score is bounded between 0 and 1, making many values seem similar, while the logit of the propensity score is unbounded. This transformation essentially stretches the distribution, while preserving ranks.

### Caliper

To ensure that we do not accept any bad matches, a caliper (maximum distance that we are willing to tolerate) can be used. In practice, a common choice for a cliper is the 0.2 times the standard deviation of logit of the propensity score. In detail:

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
