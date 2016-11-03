---
title       : Multinomial Models 
subtitle    : Logit / Probit / Conditional Logit
author      : William Reed
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : mathjax            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
github:
user: strandvej
repo: slides


--- 

## Models To Be Considered

 > - Multinomial Logit
 > - Multinomial Probit
 > - Conditional Logit

--- .build 

## Multinomial Logit


$$Pr(y_i=m|\mathbf{x_i})=\frac{exp(\mathbf{x_i\beta_m})}{\sum_{j=1}^J exp(\mathbf{x_i\beta_j})}$$

In this model there are $m$ categories of outcomes such as:

1.  Vote Clinton
2.  Vote Trump
3.  Vote Other
4.  Don't Vote

There are $j$ vectors of coefficients $\mathbf{\beta_j}$, and there are $i$ observations
of a matrix of $\mathbf{x}$ independent variables.



---
## Identification

> - As in the ordered logit/probit, the multinomial logit is not identified
without a constraint.  

> - Typically, the analyst chooses one of the categories
and compares all other outcomes to this "excluded" or "reference catetory":

> - Assume that on vector of the coefficients equals a constant, e.g. ($\beta_1=0$).

> - $$Pr(y_i=m|\mathbf{x_i})=\frac{exp(\mathbf{x_i\beta_m})}{\sum_{j=1}^J exp(\mathbf{x_i\beta_j})}$$ where $$\mathbf{\beta_1}=0$$

---

---
## Random Utility Interpretation

> - McFadden and Luce (1959)
> - Individuals choose the outcom that maximizes their utility
> - Utility for choice 1 is $u_1$ and for choice 2 the utility is $u_2$
> - Choose 1 if $u_1>u_2$.  Choose 2 if $u_2>u_1$
> - generalizing this to $m$ choices for individual $i$: $u_{im}=u_{im}+\epsilon_{im}$
> - $Pr(y_i)=1=Pr(u_{i1}>u_{i2})$
> - $=Pr(u_{i1}+\epsilon_{i1}>u_{i2}+\epsilon_{i2})$
> - $=Pr(\epsilon_{i1}-\epsilon_{i2}>u_{i1}-u_{i2})$
> - When the error terms are independent and following a type I extereme-value
distirbution, MacFadden (1973) proves that this is the multinomial logit model specification.

---
 
 
--- 
## R Examples

The data includes 200 students. The DV is __prog__, program type. The predictor variables are social economic status, **ses**, and writing score, **write**.

```
## Loading required package: foreign
```

```
## Loading required package: nnet
```

```r
with(ml, table(ses, prog))
```

```
##         prog
## ses      general academic vocation
##   low         16       19       12
##   middle      20       44       31
##   high         9       42        7
```
---

---

```r
with(ml, do.call(rbind, tapply(write, prog,
  function(x) c(M = mean(x), SD = sd(x)))))
```

```
##                 M       SD
## general  51.33333 9.397775
## academic 56.25714 7.943343
## vocation 46.76000 9.318754
```
---

---

```r
ml <- read.dta("http://www.ats.ucla.edu/stat/data/hsbdemo.dta")
ml$prog2 <- relevel(ml$prog, ref = "academic")
test <- multinom(prog2 ~ ses + write, data = ml)
```

```
## # weights:  15 (8 variable)
## initial  value 219.722458 
## iter  10 value 179.982880
## final  value 179.981726 
## converged
```
--- 

---
Estimated Coefficients

```r
test
```

```
## Call:
## multinom(formula = prog2 ~ ses + write, data = ml)
## 
## Coefficients:
##          (Intercept)  sesmiddle    seshigh      write
## general     2.852198 -0.5332810 -1.1628226 -0.0579287
## vocation    5.218260  0.2913859 -0.9826649 -0.1136037
## 
## Residual Deviance: 359.9635 
## AIC: 375.9635
```
---

---
Test statistics


```r
z <- summary(test)$coefficients/summary(test)$standard.errors
z
```

```
##          (Intercept)  sesmiddle   seshigh     write
## general     2.445214 -1.2018081 -2.261334 -2.705562
## vocation    4.484769  0.6116747 -1.649967 -5.112689
```
P-values

```r
p <- (1 - pnorm(abs(z), 0, 1))*2
p
```

```
##           (Intercept) sesmiddle    seshigh        write
## general  0.0144766100 0.2294379 0.02373856 6.818902e-03
## vocation 0.0000072993 0.5407530 0.09894976 3.176045e-07
```
---

---
## Substantive Effects

```r
require(foreign)
require(nnet)
ml <- read.dta("http://www.ats.ucla.edu/stat/data/hsbdemo.dta")
ml$prog2 <- relevel(ml$prog, ref = "academic")
test <- multinom(prog2 ~ ses + write, data = ml)
```

```
## # weights:  15 (8 variable)
## initial  value 219.722458 
## iter  10 value 179.982880
## final  value 179.981726 
## converged
```
---

---
What can we extract from the text object?


```r
coef(test)
```

```
##          (Intercept)  sesmiddle    seshigh      write
## general     2.852198 -0.5332810 -1.1628226 -0.0579287
## vocation    5.218260  0.2913859 -0.9826649 -0.1136037
```
---

---
What can we extract from the text object?


```r
vcov(test)
```

```
##                      general:(Intercept) general:sesmiddle general:seshigh
## general:(Intercept)           1.36058440     -0.0939552365   -0.0598129405
## general:sesmiddle            -0.09395524      0.1968983117    0.1225346766
## general:seshigh              -0.05981294      0.1225346766    0.2644218303
## general:write                -0.02383242     -0.0005216316   -0.0011822727
## vocation:(Intercept)          0.66778784     -0.0445133860   -0.0380738331
## vocation:sesmiddle           -0.06677794      0.0918050124    0.0636262847
## vocation:seshigh             -0.06772180      0.0641290729    0.0993141913
## vocation:write               -0.01099928     -0.0003719111   -0.0004612205
##                      general:write vocation:(Intercept) vocation:sesmiddle
## general:(Intercept)  -2.383242e-02           0.66778784      -6.677794e-02
## general:sesmiddle    -5.216316e-04          -0.04451339       9.180501e-02
## general:seshigh      -1.182273e-03          -0.03807383       6.362628e-02
## general:write         4.584297e-04          -0.01124769       7.442145e-05
## vocation:(Intercept) -1.124769e-02           1.35385215      -1.078580e-01
## vocation:sesmiddle    7.442145e-05          -0.10785795       2.269321e-01
## vocation:seshigh      8.438874e-05          -0.12863978       1.601997e-01
## vocation:write        2.039017e-04          -0.02425362      -1.062758e-03
##                      vocation:seshigh vocation:write
## general:(Intercept)     -6.772180e-02  -0.0109992808
## general:sesmiddle        6.412907e-02  -0.0003719111
## general:seshigh          9.931419e-02  -0.0004612205
## general:write            8.438874e-05   0.0002039017
## vocation:(Intercept)    -1.286398e-01  -0.0242536224
## vocation:sesmiddle       1.601997e-01  -0.0010627581
## vocation:seshigh         3.546995e-01  -0.0006283577
## vocation:write          -6.283577e-04   0.0004937265
```
---


---
## Things to Consider

> - Can outcomes be combined
> - Likelihood Ratio Test
> - Let's think this through

---


---
## Testing for IIA

```r
library(mlogit)
```

```
## Loading required package: Formula
```

```
## Loading required package: maxLik
```

```
## Loading required package: miscTools
```

```
## 
## Please cite the 'maxLik' package as:
## Henningsen, Arne and Toomet, Ott (2011). maxLik: A package for maximum likelihood estimation in R. Computational Statistics 26(3), 443-458. DOI 10.1007/s00180-010-0217-1.
## 
## If you have questions, suggestions, or comments regarding the 'maxLik' package, please use a forum or 'tracker' at maxLik's R-Forge site:
## https://r-forge.r-project.org/projects/maxlik/
```
---

---

```r
ml_1<-mlogit.data(ml,shape="wide",choice="prog")
test <- mlogit(prog ~ 1 | ses + write,  data = ml_1, 
               reflevel="academic", probit=FALSE)
```
---


---

```r
test
```

```
## 
## Call:
## mlogit(formula = prog ~ 1 | ses + write, data = ml_1, reflevel = "academic",     probit = FALSE, method = "nr", print.level = 0)
## 
## Coefficients:
##  general:(intercept)  vocation:(intercept)     general:sesmiddle  
##             2.852186              5.218200             -0.533291  
##   vocation:sesmiddle       general:seshigh      vocation:seshigh  
##             0.291393             -1.162832             -0.982670  
##        general:write        vocation:write  
##            -0.057928             -0.113603
```
---



---
## Testing for IIA

> - Hausman and McFadden (1984)
> - Logic of this test
> - Implementation

---


---
## Still to Consider

> - Multinomial Probit
> - Conditional Logit
> - Mixed Models

---
