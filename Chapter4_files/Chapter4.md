---
title: "Chapter4"
author: "Munir Nur"
date: "4/25/2019"
output: 
  html_document: 
    keep_md: yes
---


```r
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
```

```
## ── Attaching packages ──────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
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
## ── Conflicts ─────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
#library(rethinking)
```


#### 4E1 In the model definition below, which line is the likelihood?

$y_i \sim Normal(\mu, \sigma)$


#### 4E2 In the model definition just above, how many parameters are in the posterior distribution?

2, parameters $\mu$ and $\sigma$


#### 4E3 Using the model definition above, write down the appropriate form of Bayes’ theorem that
includes the proper likelihood and priors.

not sure but: $P(\mu , \sigma | y) = \frac{P(y_i \sim Normal(\mu, \sigma)) * P(\mu \sim Normal(0,10)) * P(\sigma \sim Uniform(0,10))}{P(prior\ probability\ of\ y)}$


#### 4E4. In the model definition below, which line is the linear model?

$ \mu _i ~ \alpha + \beta x_i$

#### 4E5. In the model definition just above, how many parameters are in the posterior distribution?

2, parameters $\alpha$ and $\beta$


##4M1. For the model definition below, simulate observed heights from the prior (not the posterior).


```r
# sample sigma from a uniform dist from 0, 10
sigma_s <- runif(1000, 0, 10)
mu_s <- rnorm(1000, 0, 10)

prior_y_distribution <- rnorm(1000, mean=mu_s, sd=sigma_s)
plot(density(prior_y_distribution))
```

![](Chapter4_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

#### 4M2. Translate the model justabove into a quap formula

```r
flist <- alist(
    y ~ dnorm( mu_s, sigma_s ) ,
    mu ~ dnorm( 0 , 10 ) ,
    sigma ~ dunif( 0 , 10 )
)
```

#### 4M3. Translate the quap model formula below into a mathematical model definition.

flist <- alist(
    y ~ dnorm( mu , sigma ),
    mu <- a + b*x,
    a ~ dnorm( 0 , 50 ),
    b ~ dunif( 0 , 10 ),
    sigma ~ dunif( 0 , 50 )
)

$$

y_i \sim Normal( \mu, \sigma ) \\

\mu = \alpha + \beta xi \\

\alpha \sim Normal(0, 50) \\

\beta \sim Normal(0,10) \\

\sigma \sim Uniform(0, 50)

$$


# 4M4, 4M5, 4M6, 4H1, 4H2

#### 4M4

A sample of students is measured for height each year for 3 years. After the third year, you want to fit a linear regression predicting height using year as a predictor. Write down the mathematical model definition for this regression, using any variable names and priors you choose. Be prepared to defend your choice of priors.

$$

y_i \sim Normal( \mu_i, \sigma ) \\

\mu = \alpha + \beta xi \\

\alpha \sim Normal(150, 30) \\

\beta \sim log\ normal(0,10) \\

\sigma \sim Uniform(0, 10)

$$

#### 4M5

Now suppose I tell you that the average height in the first year was 120 cm and that every student got taller each year. Does this information lead you to change your choice of priors? How?

$$

y_i \sim Normal( \mu_i, \sigma ) \\

\mu = \alpha + \beta xi \\

\alpha \sim Normal(120, 30) \\

\beta \sim log\ normal(0,10) \\

\sigma \sim Uniform(0, 10)

$$

#### 4M6

Now suppose I tell you that the variance among heights for students of the same age is never more than 64cm. How does this lead you to revise your priors?

-> change SD such that SD^2 <= 64:

$$

y_i \sim Normal( \mu_i, \sigma ) \\

\mu = \alpha + \beta xi \\

\alpha \sim Normal(120, 30) \\

\beta \sim log\ normal(0,10) \\

\sigma \sim Uniform(0, 8)

$$

#### 4H1. 

The weights listed below were recorded in the !Kungcensus, but height swere not recorded for these individuals. Provide predicted heights and 89% intervals (either HPDI or PI) for each of these individuals. That is, fill in the table below, using model-based predictions.



Individual   weight   expected height   89% interval
1             46.95
2             43.72
3             64.78
4             32.59
5             54.63
    

```r
weights <- c(46.95, 43.72, 64.78, 32.59, 54.63)
```
         
