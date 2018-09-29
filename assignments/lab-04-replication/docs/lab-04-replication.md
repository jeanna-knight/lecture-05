Lab-04 Replication
================
Christopher Prener, Ph.D.
(September 29, 2018)

## Introduction

This file contains the replication code for Lab-04, which focuses on
understanding the distribution of random variables and normality testing
in `R`.

## Dependencies

This lab requires the `testDriveR` package for data and the `moments`
and `nortest` packages for normality testing:

``` r
# tidyverse
library(ggplot2)

# data
library(testDriveR)

# statistics
library(moments)
library(nortest)
```

## Part 1 - Binomial Distribution

### Question 1

The following function finds the probability under the binomial
distribution for more than *k* outcomes:

``` r
pbinom(24, size = 250, prob = .4, lower.tail = FALSE)
```

    ## [1] 1

The probability of more than 24 successes in a series of 250 trials
where the probability of success is \(p=0.4\) is nearly 1. `R` rounds
the reported probability in this case.

### Question 2

The following function finds the probability under the binomial
distribution for *k* or fewer outcomes:

``` r
pbinom(25, size = 250, prob = .4, lower.tail = TRUE)
```

    ## [1] 2.706299e-26

The scientific notation reported here means that the probability of 25
or fewer successes in a series of 250 trials where the probability of
success is \(p=0.4\) is nearly 0.

### Question 3

The following function finds the probability under the binomial
distribution for exactly *k* outcomes:

``` r
dbinom(25, size = 250, prob = .4)
```

    ## [1] 2.26111e-26

The scientific notation reported here means that the probability of
exactly 25 successes in a series of 250 trials where the probability of
success is \(p=0.4\) is nearly 0.

## Part 2 - Poisson Distribution

### Question 4

The following function finds the probability under the poisson
distribution for more than *k* outcomes. Note that lambda (\(\lambda\))
is the product of \(n*p\):

``` r
lambda = 1000 * .025
ppois(4, lambda = lambda, lower.tail = FALSE)
```

    ## [1] 0.9999997

The probability of observing more than 4 failures is \(p=0.999\), or
nearly 1. This means that we are almost certain to see more than 4
rockets fail.

### Question 5

The following function finds the probability under the poisson
distribution for more than *k* outcomes. This question relies on the
\(\lambda\) calculation from the previous question.

``` r
dpois(18, lambda = lambda)
```

    ## [1] 0.03156582

The probability of observing exactly 18 failures is \(p=0.032\). In
other words, it is highly unlikely that we would see exactly 18
failures.

### Question 6

The following function finds the probability under the poisson
distribution for *k* or fewer outcomes. This question relies on the
\(\lambda\) calculation from the previous question.

``` r
ppois(15, lambda = lambda, lower.tail = TRUE)
```

    ## [1] 0.02229302

The probability of observing 15 or fewer failures is \(p=0.022\). Like
the previous probabiliy, it is highly unlikely that we would see this
outcome.

## Part 3 - Normal Distribution

### Question 7

This question has two pieces that need to be addressed. The code below
calculates the z-score and then looks up the cumulative probability of
that z-score under the standard normal distribution. We use the formula
\(z = \frac{x-\bar{x}}{s}\) to calculate the z-score.

``` r
zScore <- (25-21)/3
pnorm(zScore, mean = 0, sd = 1, lower.tail = TRUE)
```

    ## [1] 0.9087888

Based on the probability of \(p=0.909\), this outcome is less likely
than 90% of the outcomes in this distribution. This makes the
probability of drawing a single participant whose score is 25 low, but
not impossible.

## Part 4 - Skew and Kurtosis by Hand

See the `pdf` in the `Week-05/Lab/` repo for the replication by hand.

We can check our results using the following code. First, we need to
read the vector of values from the assignment sheet into `R`:

``` r
q8 <- c(1,4,3,2,4,2,1,4,3,3)
```

The skewness of `q8` is:

``` r
skewness(q8)
```

    ## [1] -0.2885049

Given that the skew value is \(sk=-0.289\). This indicates some minor
negative skew. In other words, it is a distribution with a long left
tail. Since it is within the -2 to 2 range, we have a distribution that
does not appear to depart significantly from the standard normal.

The kurtosis of `q8` is:

``` r
kurtosis(q8)
```

    ## [1] 1.760604

Given the kurtosis value of \(k=1.761\), we have an indication of a
platykurtic distribution since it is less than 3. This does not give us
any concerns about the distribution since it is less than 5.

## Part 5 - Normality Testing in `R`

### Question 9

The following code loads the needed data from `testDriveR` into a data
frame:

``` r
autoData <- auto17
```

#### Question 9a - Skew

The following function will report the skew of `fuelCost`:

``` r
skewness(autoData$fuelCost)
```

    ## [1] 0.7116702

Given that the skew value is \(sk=0.712\). This indicates some minor
positive skew. In other words, it is a distribution with a long right
tail. Since it is within the -2 to 2 range, we have a distribution that
does not appear to depart significantly from the standard normal.

#### Question 9b - Kurtosis

The following function will report the kurtosis of `fuelCost`:

``` r
kurtosis(autoData$fuelCost)
```

    ## [1] 4.047727

Given the kurtosis value of \(k=4.048\), we have an indication of a
slightly leptokurtic distribution since it is greater than 3. This does
not give us any concerns about the distribution since it is less than 5.

#### Question 9c - Q-Q Plot

The following pair of functions creates a plot with both the q-q
distribution and the q-q line:

``` r
ggplot(data = autoData, mapping = aes(sample = fuelCost)) +
  stat_qq() +
  stat_qq_line()
```

![](lab-04-replication_files/figure-gfm/qq-fuel-1.png)<!-- -->

The plot shows some deviations from normal in both tails. You can see
the right skew by looking at the values on the right side of the
distribution, which appear to have a greater departure from normal than
those on other areas of the distribution.

#### Question 9d - Shapiro-Francia Test

The following function produces the hypothesis tests results for the
Shapiro-Francia test:

``` r
sf.test(autoData$fuelCost)
```

    ## 
    ##  Shapiro-Francia normality test
    ## 
    ## data:  autoData$fuelCost
    ## W = 0.96974, p-value = 7.183e-14

The results of the Shapiro-Francia test (W’ = 0.970, p \< 0.001) suggest
that the distribution does have some departures from normal. These
results confict with the findings of the q-q plot, the skew, and
kurtosis, which all point to a slightly non-normal distribution that
does not depart so far from normal as to be problematic. This is an
example of the competing information that I discussed in class. In this
case, I would lean away from the results of the Shapiro-Francia test and
towards the other three sources of information since they all point in
the “same direction” towards an only slightly abnormal distribution.
