---
title: "Comparison Between Two Groups"
teaching: 45
exercises: 10
---

::::::::::::::::::::::::::::::::::::: questions

- Do two sample groups differ for a continuous trait?

::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: objectives

- Tests for use with comparison of two groups of continuous data
- Summarising continuous data graphically
- Selecting and using relevant statistical tests

::::::::::::::::::::::::::::::::::::: 




## Comparison of two sample groups

Earlier we discussed continuous data, and how to investigate relationships 
(correlations) between two continuous variables. In this section, we will learn 
how to identify whether a single continuous trait differs between two sample 
groups - a two sample test. Specifically, we will investigate whether there is a
statistically-significant difference between the distribution of that variable 
between the two groups. As an example, we will test whether male patients in our
gallstones study are taller than female patients.

::::::::::::::::::::::::::::::::::::: discussion

## Discussion

See if you can identify other examples where you might use a two sample groups
comparison. Do you have any in your own research?

::::::::::::::::::::::::::::::::::::: 

### Choosing the relevant test

As with testing for categorical variables, there are a range of different 
statistical analyses for two sample group comparisons; the appropriate one to 
use is determined by the nature of the dataset. There are two primary questions
we need to ask to identify the relevant test: are the two datasets 
normally-distributed, and are the data paired (that is, are there repeated
measurements on the same samples)? The figure below summarises the choice of
statistical test used for each of these cases.

![](fig/05-fig1.png){alt="Decision tree to determine appropriate test to use. 
Question one - Are the data normally distributed? Question 2 - Are the data paired?
If the data are both normally distributed and paired, us a paired T-test
If the data are normally distributed but not paired, use a T-test
If the data are not normally distributed but are paired, use the Wilcoxon signed rank test
If the data are neither normally distributed nor paired, use the Mann-Whitney test"}

The first step is to determine whether the continuous variable in each group is
normally distributed. We've already learned about the `shapiro.test` function to 
test for normality, and can use that again in this situation.

The second decision is to identify whether the data is paired or not. Paired 
data is when the two groups are the same test samples but measured under 
different conditions (for example, a group of patients tested before and after 
treatment), unpaired is when the two groups are independent (for example, two 
separate groups of patients, one group treated and one untreated). 

There are a few further subtleties beyond this which we will come to in a 
moment, but these are the two major determining factors in choosing the correct
test.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1

In our gallstones dataset, assume that BMI is normally distributed for
patients with a recurrence of gallstones and not normal for those with no 
recurrence. Which test would we use to investigate whether those two groups
(with and without recurrence) had different BMIs?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 1

One data set is normally distributed, the other is not, so we choose the 
option for non-normally distributed data - the branch to the right (we can 
only answer yes to the first question if both datasets are normal). The data
is not paired - the patients with recurrence are a different group to those
without. In this case we would use the Mann-Whitney test.

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

## Two sample Student's T-test

If data is normally distributed for **both** groups, we will generally use the 
Student's T-test. This compares the means of two groups measured on the same 
continuous variable. Tests can be two-sided (testing whether the groups are not
equal) or one-sided (testing either whether the second group is greater than or
less that the first). As we discussed in the introduction, generally a two-sided
test is preferred unless there is a specific reason why a single-sided one is
justified.

| | | |
| ---------: | :------: | --------- |
| H<sub>0</sub>:&nbsp;µ<sub>1</sub>&nbsp;=&nbsp;µ<sub>2</sub> | against | H<sub>1</sub>:&nbsp;µ<sub>1</sub>&nbsp;≠&nbsp;µ<sub>2</sub>&nbsp;(two&#8209;sided) |
| | or | |
| H<sub>0</sub>:&nbsp;µ<sub>1</sub>&nbsp;<=&nbsp;µ<sub>2</sub> | against | H<sub>1</sub>:&nbsp;µ<sub>1</sub>&nbsp;>&nbsp;µ<sub>2</sub>&nbsp;(greater) |
| | or | |
| H<sub>0</sub>:&nbsp;µ<sub>1</sub>&nbsp;>=&nbsp;µ<sub>2</sub> | against | H<sub>1</sub>:&nbsp;µ<sub>1</sub>&nbsp;<&nbsp;µ<sub>2</sub>&nbsp;(less) |

If **equal variance**: Student's T-test  
If **unequal variance**: Welch's two-sample T-test  
If **data are paired**: Student's paired T-test  

::::::::::::::::::::::::::::::::::::: callout

## Multiple tests in one

The R `t.test` function combines all three of these tests, and defaults to 
Welch's two-sample T-test. To perform a standard T-test, use the parameter 
setting `var.equal = TRUE`, and for a paired T-test, use `paired = TRUE`. 

::::::::::::::::::::::::::::::::::::: 

## Two sample Mann-Whitney test

Unless **both** groups are normally distributed, use the Mann-Whitney test. This
is a non-parametric test analogous to the unpaired T-test, used when the 
_dependent_ variable is non-normally distributed

The Mann-Whitney test compares the medians of the two groups rather than the 
means, by considering the data as rank order values rather than absolute values.

::::::::::::::::::::::::::::::::::::: callout

## Another multiple-test function
The `wilcox.test` function in R defaults to unpaired data - effectively 
returning the Mann-Whitney test instead. Carry out a paired Wilcox test with 
the `paired = TRUE` argument

::::::::::::::::::::::::::::::::::::: 

## Two sample test example

Is there a difference in height between females and males in the gallstones 
dataset? 

Height: Continuous variable  
Gender: Categorical variable with two levels  
Null hypothesis: There is no difference in height between the groups  

_Step one - visualise the data_  
We will start by reviewing the data using a boxplot to see if there is an 
indication of difference between the groups

```r
plot(gallstones$Height ~ gallstones$Gender, 
     col=c('red','blue'),
     ylab = 'Height',
     xlab = 'Gender')
```

<img src="fig/05-Continuous-Variable-rendered-unnamed-chunk-1-1.png" style="display: block; margin: auto;" />

Visually there certainly appears to be a difference. But is it statistically
significant?

_Step two - is the data normally distributed?_  

```r
par(mfrow=c(1,2))
hist(gallstones$Height[which(gallstones$Gender == 'F')], main = "Histogram of heights of females", xlab = "")
hist(gallstones$Height[which(gallstones$Gender == 'M')], main = "Histogram of heights of males", xlab = "")
```

<img src="fig/05-Continuous-Variable-rendered-unnamed-chunk-2-1.png" style="display: block; margin: auto;" />

```r
par(mfrow=c(1,1))
```

This doesn't look very normally-distributed, but we do have relatively few data
points. A more convincing way to determine this would be with the Shapiro-Wilks
test

```r
by(gallstones$Height, gallstones$Gender, shapiro.test)
```

```{.output}
gallstones$Gender: F

	Shapiro-Wilk normality test

data:  dd[x, ]
W = 0.94142, p-value = 0.2324

------------------------------------------------------------ 
gallstones$Gender: M

	Shapiro-Wilk normality test

data:  dd[x, ]
W = 0.88703, p-value = 0.05001
```

Neither test gives a significant p-value, so in the absence of sufficient 
evidence to accept the alternative hypothesis of non-normality, we treat the
data as if it were normal; that is, we use a T-test 

_Step three - are variances equal?_  

```r
# A quick and dirty test - how similar are the standard deviations?
by(gallstones$Height, gallstones$Gender, sd)
```

```{.output}
gallstones$Gender: F
[1] 5.518799
------------------------------------------------------------ 
gallstones$Gender: M
[1] 9.993331
```

```r
# Or properly test for equality of variance using Levene's test
library(DescTools)
```

```{.output}
Registered S3 method overwritten by 'DescTools':
  method         from 
  reorder.factor gdata
```

```r
LeveneTest(gallstones$Height ~ gallstones$Gender)
```

```{.output}
Levene's Test for Homogeneity of Variance (center = median)
      Df F value  Pr(>F)  
group  1  3.4596 0.07131 .
      35                  
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Although the standard deviations of the two groups (and hence the variances) 
seem to be quite different, Levene's test gives a non-significant p-value of 0.07.
This means that we shouldn't reject the null hypothesis of equal variance, and
so we should perform a Student's T-test. If the variances had been different, 
then we would have used Welch's two-sample T-test instead.

_Step four - carry out a T-test_  

```r
# Specify equal variance using the var.equal = TRUE argument.
# var.equal would be set to FALSE if the p-value of the Levene's test was less 
# than 0.05, and the `t.test` function would then run a Welch's two-sample test.
t.test(gallstones$Height ~ gallstones$Gender, var.equal = TRUE)
```

```{.output}

	Two Sample t-test

data:  gallstones$Height by gallstones$Gender
t = -3.6619, df = 35, p-value = 0.00082
alternative hypothesis: true difference in means between group F and group M is not equal to 0
95 percent confidence interval:
 -14.655702  -4.201441
sample estimates:
mean in group F mean in group M 
       160.5714        170.0000 
```

**Conclusion**: the p-value is significant so we can accept the alternative 
hypothesis and conclude that there is a difference in the mean height of males
and females in our dataset.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2

Using the gallstones dataset, test whether the gallstone diameter ("Diam") is
different between patients who suffer a recurrence and those who do not.

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 2


```r
# Visualise data
boxplot(gallstones$Diam ~ gallstones$Rec, col = c("red","blue"),
     ylab = "Diameter",
     xlab = "Recurrence")
# Test whether data is normally distributecd
by(gallstones$Diam, gallstones$Rec, hist)
by(gallstones$Diam, gallstones$Rec, shapiro.test)
```
Data is not normal for the recurrence group, and data is not paired - hence
Mann-Whitney test

```r
# Use wilcox.test function which defaults to Mann-Whitney analysis
wilcox.test(gallstones$Diam ~ gallstones$Rec)
```
The p-value is not significant, so we do not have sufficient evidence to 
reject the null hypothesis that there is no difference in gallstone size
between the two groups.

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

## Group descriptions

If there is a significant difference between the two groups (or even if there
isn't) it is often useful to generate some summary statistics for each group. 
We can do this with the `by` command, which we've used already in this section, 
combined with summary functions


```r
# For normally distributed data, report the mean and standard deviation
by(gallstones$Height, gallstones$Gender, mean)
```

```{.output}
gallstones$Gender: F
[1] 160.5714
------------------------------------------------------------ 
gallstones$Gender: M
[1] 170
```

```r
by(gallstones$Height, gallstones$Gender, sd)
```

```{.output}
gallstones$Gender: F
[1] 5.518799
------------------------------------------------------------ 
gallstones$Gender: M
[1] 9.993331
```


```r
# For non-normally distributed data, report the median and inter-quartile range
by(gallstones$Diam, gallstones$Rec, median)
```

```{.output}
gallstones$Rec: NoRecurrence
[1] 10
------------------------------------------------------------ 
gallstones$Rec: Recurrence
[1] 8.5
```

```r
by(gallstones$Diam, gallstones$Rec, IQR)
```

```{.output}
gallstones$Rec: NoRecurrence
[1] 12
------------------------------------------------------------ 
gallstones$Rec: Recurrence
[1] 9
```

```r
# Many of the summary statistics can be calculated in one step with the FSA 
# Summarize function
library(FSA)
```

```{.output}
## FSA v0.9.3. See citation('FSA') if used in publication.
## Run fishR() for related website and fishR('IFAR') for related book.
```

```r
Summarize(gallstones$Height~gallstones$Gender)
```

```{.output}
  gallstones$Gender  n     mean       sd min  Q1 median     Q3 max
1                 F 21 160.5714 5.518799 147 157  161.0 163.00 170
2                 M 16 170.0000 9.993331 156 164  167.5 175.25 189
```

```r
Summarize(gallstones$Diam~gallstones$Rec)
```

```{.output}
  gallstones$Rec  n     mean       sd min Q1 median Q3 max
1   NoRecurrence 21 12.42857 7.440238   3  6   10.0 18  27
2     Recurrence 16 10.06250 6.180278   4  5    8.5 14  26
```

## Paired samples
If data is paired, that is, it is the same samples under two different 
conditions, we can take advantage of that to carry out statistical tests with 
greater discriminatory power. That is because by using paired samples, we remove
a lot of the noise that can otherwise obscure our results. Paired data must have
the same number of results in each group, there must be a one-to-one relationship
between the groups (every sample that appears in one group must appear in the 
other), and the data must be the same sample order in each group.

Otherwise, paired sample analysis is performed in a similar way to unpaired 
analysis. The main difference is to add the `paired = TRUE` argument to the 
`t.test` or `wilcox.test` function.

::::::::::::::::::::::::::::::::::::: keypoints

- Use `hist` and boxplots to review distribution of variables for a group
- Summarise grouped data using the `by` command
- Distinguish paired and non-paired samples
- Correctly use the `t.test` and `wilcox.test` functions

::::::::::::::::::::::::::::::::::::: 

[r-markdown]: https://rmarkdown.rstudio.com/
