---
title: "Chapter4"
author: "Munir Nur"
date: "5/10/2019"
output: 
  html_document: 
    keep_md: yes
---


```r
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
```

```
## ── Attaching packages ────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 3.1.0       ✔ purrr   0.3.2  
## ✔ tibble  2.1.1       ✔ dplyr   0.8.0.1
## ✔ tidyr   0.8.3       ✔ stringr 1.3.1  
## ✔ readr   1.3.1       ✔ forcats 0.4.0
```

```
## Warning: package 'tibble' was built under R version 3.5.2
```

```
## Warning: package 'tidyr' was built under R version 3.5.2
```

```
## Warning: package 'purrr' was built under R version 3.5.2
```

```
## Warning: package 'dplyr' was built under R version 3.5.2
```

```
## Warning: package 'forcats' was built under R version 3.5.2
```

```
## ── Conflicts ───────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(rethinking)
```

```
## Loading required package: rstan
```

```
## Loading required package: StanHeaders
```

```
## Warning: package 'StanHeaders' was built under R version 3.5.2
```

```
## rstan (Version 2.18.2, GitRev: 2e1f913d3ca3)
```

```
## For execution on a local, multicore CPU with excess RAM we recommend calling
## options(mc.cores = parallel::detectCores()).
## To avoid recompilation of unchanged Stan programs, we recommend calling
## rstan_options(auto_write = TRUE)
```

```
## 
## Attaching package: 'rstan'
```

```
## The following object is masked from 'package:tidyr':
## 
##     extract
```

```
## Loading required package: parallel
```

```
## rethinking (Version 1.88)
```

```
## 
## Attaching package: 'rethinking'
```

```
## The following object is masked from 'package:purrr':
## 
##     map
```


#### 5E1. Which of the linear models below are multiple linear regressions?

-> (2) and (4) are multiple llinear regressions, they use multiple predictor variables to estimate $\mu_i$

#### 5E3. Write down a multiple regression to evaluate the claim: Neither amount of funding nor size of laboratory is by itself a good predictor of time to PhD degree; but together these variables are both positively associated with time to degree. Write down the model definition and indicate which side of zero each slope parameter should be on.

$$
time_i ~ Normal(\mu_i, \sigma) \\

\mu_i = \alpha + \beta_f Fund_i + \beta_s Size_i \\

\alpha \sim Normal(4, 2) \\

\beta_f \sim Normal(0,1) \\

\beta_s \sim Normal(0,1) \\

\sigma \sim Exponential(1)
$$

#### 5M2. Invent your own example of a masked relationship. An outcome variable should be correlated with both predictor variables, but in opposite directions. And the two predictor variables should be correlated with one another.

To predict the amount of stray cats in a college town, can use the number of students enrolled and the average cost for an apartment.

As the amount of students enrolled goes up, the number of stray cats should decrease.
As the price of rent goes down, the number of stray cats should decrease.

Ideally, if the average cost for an apartment goes down, the amount of students enrolled might go up.


#### 5M3. It is sometimes observed that the best predictor of fire risk is the presence of firefighters— States and localities with many firefighters also have more fires. Presumably firefighters do not cause fires. Nevertheless, this is not a spurious correlation. Instead fires cause firefighters. Consider the same reversal of causal inference in the context of the divorce and marriage data. How might a high divorce rate cause a higher marriage rate? Can you think of a way to evaluate this relationship, using multiple regression?

High divorce rates cause people to be able to marry again, allowing them to contribute multiple times to the count. 

Can evaluate the relationship by predicting Marriage rate by using using Marriage age and Divorce rate as predictors.

#### 5M4. In the divorce data, States with high numbers of Mormons (members of The Church of Jesus Christ of Latter-day Saints, LDS) have much lower divorce rates than the regression models expected. Find a list of LDS population by State and use those numbers as a predictor variable, predicting divorce rate using marriage rate, median age at marriage, and percent LDS population (possibly stan- dardized). You may want to consider transformations of the raw percent LDS variable.


```r
library(rvest)
```

```
## Warning: package 'rvest' was built under R version 3.5.2
```

```
## Loading required package: xml2
```

```
## 
## Attaching package: 'rvest'
```

```
## The following object is masked from 'package:purrr':
## 
##     pluck
```

```
## The following object is masked from 'package:readr':
## 
##     guess_encoding
```

```r
waffle_data <- data("WaffleDivorce")

# scrape wikipedia data
theurl <- "https://en.wikipedia.org/wiki/The_Church_of_Jesus_Christ_of_Latter-day_Saints_membership_statistics_(United_States)"
file<-read_html(theurl)
tables<-html_nodes(file, "table")
table1 <- html_table(tables[2], fill = TRUE)

# import LDS percentage column, convert to numeric
state_percents <- table1[[1]]$LDS[1:51]

# remove missing data (Nevada), Waffle House has 50 while wikipedia has 51. 
# should be 51, since both include DC
noNevada <- state_percents[-29]
state_percents_no_comma <- (gsub("%", "", noNevada))
state_percents_no_comma <- as.numeric(state_percents_no_comma)

# scale with 0 mean
scaled_lds <- scale(state_percents_no_comma)

# get model variables
mean_lds_percent <- mean(scaled_lds)
std_lds_percent <- sd(scaled_lds)

mean_waffle_mage <- mean(scale(WaffleDivorce$MedianAgeMarriage))
std_waffle_mage <- sd(scale(WaffleDivorce$MedianAgeMarriage))

mean_waffle_marriage <- mean(scale(WaffleDivorce$Marriage))
std_waffle_marriage <- sd(scale(WaffleDivorce$Marriage))

# model_5m4 <- quap(
#   alist(
#     divorce ~ dnorm(mu, sigma),
#     mu <- alpha + b1*std_waffle_mage + b2*std_waffle_marriage + b3*std_lds_percent,
#     a ~ dnorm(0, 0.5),
#     b1 ~ dnorm(0, 0.5),
#     b2 ~ dnorm(0, 0.5),
#     b3 ~ dnorm(0, 1),
#     sigma ~ dexp(1)),
#   data = d)
# precis(mod5M4)
```
