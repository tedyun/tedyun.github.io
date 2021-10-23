---
layout: post
title: "Causal inference course note - Week 4"
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

# Week 4. Inverse Probability of Treatment Weighting (IPTW)

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


## Data examples in R

We use the same right heart catheterization data discussed above.

### Set up

Load packages, read in data, view data:

```R
#install packages (if needed)
install.packages("tableone")
install.packages("ipw")
install.packages("sandwich")
install.packages("survey")

#load packages
library(tableone)
library(ipw)
library(sandwich) #for robust variance estimation
library(survey)

expit <- function(x) {1/(1+exp(-x)) }
logit <- function(p) {log(p)-log(1-p)}

#read in data
load(url("http://biostat.mc.vanderbilt.edu/wiki/pub/Main/DataSets/rhc.sav"))
#view data
View(rhc)
```

Create new dataset with only the variables that will be used:

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
died<-as.integer(rhc$death=='Yes')
age<-rhc$age
treatment<-as.numeric(rhc$swang1=='RHC')
meanbp1<-rhc$meanbp1

#new dataset
mydata<-cbind(ARF,CHF,Cirr,colcan,Coma,lungcan,MOSF,sepsis,
              age,female,meanbp1,treatment,died)
mydata<-data.frame(mydata)

#covariates we will use (shorter list than you would use in practice)
xvars<-c("age","female","meanbp1","ARF","CHF","Cirr","colcan",
         "Coma","lungcan","MOSF","sepsis")

#look at a table 1
table1<- CreateTableOne(vars=xvars,strata="treatment", data=mydata, test=FALSE)
## include standardized mean difference (SMD)
print(table1,smd=TRUE)
```

### Propensity score

```R
#propensity score model
psmodel <- glm(treatment ~ age + female + meanbp1+ARF+CHF+Cirr+colcan+
         Coma+lungcan+MOSF+sepsis,
               family  = binomial(link ="logit"))

## value of propensity score for each subject
ps <-predict(psmodel, type = "response")
```

### Create weights and check balance

```R
#create weights
weight<-ifelse(treatment==1,1/(ps),1/(1-ps))

#apply weights to data
weighteddata<-svydesign(ids = ~ 1, data =mydata, weights = ~ weight)

#weighted table 1
weightedtable <-svyCreateTableOne(vars = xvars, strata = "treatment", 
                                 data = weighteddata, test = FALSE)
## Show table with SMD
print(weightedtable, smd = TRUE)
```

You can also get the weighted mean directly using the following formula:

$$\frac{\sum_{i=1}^n I(A_i = 1) X_i / \pi_i}{\sum_{i=1}^n I(A_i = 1) / \pi_i}$$

```R
#to get a weighted mean for a single covariate directly:
mean(weight[treatment==1]*age[treatment==1]) / (mean(weight[treatment==1]))
```

### Marginal structural models

Fit the following MSM using IPTW:

$$E(Y_i^a) = g^{-1}(\psi_0 + \psi_1 a)$$

where
* $A$ is treatment,
* $Y$ is died,
* $g$ is the link function.

We will first use log link to get a causal relative risk. We will then use an identity link to get a causal risk difference.

Causal relative risk:

```R
#get causal relative risk. Weighted GLM
glm.obj<-glm(died~treatment,weights=weight,family=quasibinomial(link=log))
#summary(glm.obj)
betaiptw<-coef(glm.obj)
#to properly account for weighting, use asymptotic (sandwich) variance
SE<-sqrt(diag(vcovHC(glm.obj, type="HC0")))

#get point estimate and CI for relative risk (need to exponentiate)
causalrr<-exp(betaiptw[2])
lcl<-exp(betaiptw[2]-1.96*SE[2])
ucl<-exp(betaiptw[2]+1.96*SE[2])
c(lcl,causalrr,ucl)
```

Causal risk difference:

```R
#get causal risk difference
glm.obj<-glm(died~treatment,weights=weight,family=quasibinomial(link="identity"))
#summary(glm.obj)
betaiptw<-coef(glm.obj)
SE<-sqrt(diag(vcovHC(glm.obj, type="HC0")))

causalrd<-(betaiptw[2])
lcl<-(betaiptw[2]-1.96*SE[2])
ucl<-(betaiptw[2]+1.96*SE[2])
c(lcl,causalrd,ucl)
```

Alternatively let's fit the same models using the R package IPW:

```R
#first fit propensity score model to get weights
weightmodel<-ipwpoint(exposure= treatment, family = "binomial", link ="logit",
          denominator= ~ age + female + meanbp1+ARF+CHF+Cirr+colcan+
           Coma+lungcan+MOSF+sepsis, data=mydata)
#numeric summary of weights
summary(weightmodel$ipw.weights)
#plot of weights
ipwplot(weights = weightmodel$ipw.weights, logscale = FALSE,
         main = "weights", xlim = c(0, 22))
mydata$wt<-weightmodel$ipw.weights
```

Fitting MSMs:

```R
#fit a marginal structural model (risk difference)
msm <- (svyglm(died ~ treatment, design = svydesign(~ 1, weights = ~wt,
                  data =mydata)))
coef(msm)
confint(msm)
```

Suppose you want to truncate weights at 10. You could directly do it:

```R
truncweight<-replace(weight,weight>10,10)
#get causal risk difference
glm.obj<-glm(died~treatment,weights=truncweight,family=quasibinomial(link="identity"))
```

Or specify truncation using IPW package:

```R
# fit propensity score model to get weights, but truncated
weightmodel<-ipwpoint(exposure= treatment, family = "binomial", link ="logit",
                      denominator= ~ age + female + meanbp1+ARF+CHF+Cirr+colcan+
                        Coma+lungcan+MOSF+sepsis, data=mydata,trunc=.01)

#numeric summary of weights
summary(weightmodel$weights.trun)
#plot of weights
ipwplot(weights = weightmodel$weights.trun, logscale = FALSE,
        main = "weights", xlim = c(0, 22))
mydata$wt<-weightmodel$weights.trun
#fit a marginal structural model (risk difference)
msm <- (svyglm(died ~ treatment, design = svydesign(~ 1, weights = ~wt,
                                                    data =mydata)))
coef(msm)
confint(msm)
```
