---
title: "Multiple testing, summary, and final exercises"
teaching: 10
exercises: 45
---

::::::::::::::::::::::::::::::::::::: questions

- How do we interpret p-values when mutiple comparisons are carried out?

::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: objectives

- Recognise the impact of multiple testing on the meaning of p-values
- Adjust p-values to compensate for the effects of multiple testing 

::::::::::::::::::::::::::::::::::::: 



## Multiple testing
In ANOVA, if H<sub>0</sub> is rejected, we carry out a _post hoc_ test to 
identify which group(s) are significantly different from the other ones. If 
there are three groups (A, B and C), that means we are performing 3 comparisons:

1. A vs B
2. A vs C
3. B vs C

Each time we perform a t-test, we chose a significance threshold under which we
reject the null hypothesis. A significance level of 0.05 for example means a 5%
chance of making a type I error - incorrectly rejecting H<sub>0</sub>. When 
performing multiple tests, that probability of error applies for each individual
comparison, so the more tests performed, the higher likelihood of a type I error.

In our example three-way comparison with signficance threshold of 0.05:

* Prob(making no mistake) = 0.95 x 0.95 x 0.95 = 0.857
* Prob(making a mistake) = 1 - 0.857 = 0.143

So rather than a 5% chance of a type I error, we instead have a much higher 
14.3% chance, which is clearly not acceptable.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1

Memory test! A type I error is an incorrect rejection of the null hypothesis. 
Can you remember what a type II error is?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 1

A type II error is a failure to reject the null hypothesis when it is false

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

### Correction for multiple testing
To compensate for the effects of multiple testing on expected error rate, we 
need to adjust the p-value according to the number of hypothesis tests 
performed. 

Examples of correction approaches include:

* False Discovery Rate (FDR): controls the type I error rate
* Bonferroni correction (FWER): reduce the probability of even one false 
discovery

In R, the `p.adjust` function can be used to perform multiple-testing correction
on a vector of p-values. 

```r
# Create a vector of multiple p-values to be tested
# Note, most of these *individually* meet the significance threshold of <0.05
pval <- c(0.02, 0.01, 0.05, 0.04, 0.1, 0.6, 0.5, 0.03, 0.005, 0.0005)
# False Discovery Rate correction - only three tests remain signficant
# And round to 3 decimal places for neater display
round(p.adjust(pval, method = 'fdr'),3)
```

```{.output}
 [1] 0.050 0.033 0.071 0.067 0.125 0.600 0.556 0.060 0.025 0.005
```

```r
# Bonferroni correction - now only one remains significant
# For some reason, bonferroni defaults to 3 decimal places anyway
p.adjust(pval, method = 'bonferroni')
```

```{.output}
 [1] 0.200 0.100 0.500 0.400 1.000 1.000 1.000 0.300 0.050 0.005
```

## Summary of relevant statistical tests
This table can be used as a reference to select the appropriate test to use from
a given experimental design

|Analysis required (continuous data) |Normally distributed data |Non-normally distributed data |
|-------|---|---|
|Compare mean or median of one sample group against a known value |One sample t-test |Wilcoxon Rank Sum test |
|Compare means or medians of two sample groups (unpaired data) |Unpaired t-test |Mann-Whitney test |
|Compare means or medians of two sample groups (paired data) |Paired t-test |Wilcoxon Matched Pairs test |
|Compare means or medians of ≥ three sample groups (unpaired data) |ANOVA |Kruskal-Wallis ANOVA |
|Compare means or medians of ≥ three sample groups (paired data) |Repeated measures ANOVA |Friedman test |

|Analysis required (categorical data) |Test used |
|------|---|
|Compare two sample proportions of categorical data (unpaired data, ≤5 in one group) |Fisher exact test |
|Compare two sample proportions of categorical data (unpaired data, >5 in all groups) |Chi-square test |
|Compare two sample proportions of categorical data (paired data) |McNemar test |

## Summary of R functions

* Check for normality: `shapiro.test(var)`
* Correlation (normally distributed) `cor.test(var1,var2,method="pearson")`
* Correlation (Non-normally distributed) `cor.test(var1,var2,method="spearman")`
* Chi-square `chisq.test(table(var1,var2))`
* Fisher’s exact (≤5 counts) `fisher.test(table(var1,var2))`
* Unpaired t-test `t.test(var1~var2)`
* Paired t-test `t.test(var1~var2,paired=TRUE)`
* Wilcoxon Rank Sum test `wilcox.test(var1~var2)`
* Wilcoxon Matched Pairs Test `wilcox.test(var1~var2,paired=TRUE)`
* Compare >2 groups for one factor
* ANOVA `aov(outcome~factor1)`
* Kruskal-Wallis ANOVA `kruskal.test(outcome~factor1)`

## Final practice exercises

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2

Do patients with multiple gallstones experience more recurrences than those 
with single stones?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 2

The variables of interest are multiple stones (categorical, 2 groups)
and recurrence (categorical, 2 groups). The appropriate test is therefore
either Chi-square or Fisher's Exact, depending on the expected count for 
each category
```r
# Create the frequency table
table(gallstones$Rec,gallstones$Mult)

# Complete Cross-table
library(gmodels)
CrossTable(gallstones$Rec,gallstones$Mult,format="SPSS",prop.chisq=F,expected=T)

# Visualisation

library(ggplot2)
ggplot(gallstones, aes(Rec, fill=Mult)) + 
  geom_bar(position="fill") +
  scale_y_continuous(labels=scales::percent) +
  theme(axis.text=element_text(size=18),
        legend.text=element_text(size=18),
        legend.title=element_text(size=18),
        axis.title=element_text(size=18),
        plot.title = element_text(size=22, face="bold"))+
  ggtitle("Recurrence vs. Mult") 

# Chi-square test performed because more than 5 expected counts in each cell
chisq.test(gallstones$Rec,gallstones$Mult)
```
### Conclusion
With a p-value of 0.1295, there is no evidence for a significant association
between gallstone recurrence and the presence of multiple stones

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 3

Is the patient's age associated with gallstone recurrence?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 3

The variables of interest are gallstone recurrence (categorical, 2 groups)
and age (continuous). The appropriate test is therefore either the t-test
or Mann-Whitney test, depending on whether the data is normally distributed
or not
```r
# Test normality in both groups
shapiro.test(gallstones$Age[which(gallstones$Rec=="NoRecurrence")])
shapiro.test(gallstones$Age[which(gallstones$Rec=="Recurrence")])

# Other option
by(gallstones$Age,gallstones$Rec,shapiro.test)

# We have to perform a Mann-Whitney test 
wilcox.test(gallstones$Age~gallstones$Rec)

# Boxplots to visualise
plot(gallstones$Age~gallstones$Rec,col=c("red","blue"),ylab="Age",
     xlab="Recurrence",cex.lab=1.3)

# Statistical descriptions
by(gallstones$Age,gallstones$Rec,median)
by(gallstones$Age,gallstones$Rec,IQR)

by(gallstones$Age,gallstones$Rec,mean)
by(gallstones$Age,gallstones$Rec,sd)
```
### Conclusion
With a p-value of 0.7707, there is no evidence for a significant association
between gallstone recurrence and the age of patients

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 4

Does alcohol consumption influence the time to gallstone dissolution?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 4

The variables of interest are alcohol consumption (categorical, 3 groups)
and dissolution time (continuous). The appropriate test is therefore either
ANOVA or Kruskal-Wallis, depending on whether the data is normally 
distributed or not
```r
# Test normality in each groups
by(gallstones$Dis,gallstones$Alcohol.Consumption,shapiro.test)

# If distribution normal in each group
result<-aov(gallstones$Dis~gallstones$Alcohol.Consumption)
summary(result)

# If distribution not normal
kruskal.test(gallstones$Dis~gallstones$Alcohol.Consumption)

# Visualisation
plot(gallstones$Dis~gallstones$Alcohol.Consumption,col=2:4)
```
### Conclusion
With a p-value of 0.2389, there is no evidence for a significant association
between alcohol consumption and dissolution time

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 5

Is there any effect of treatment and/or gender on the time to dissolution?

::::::::::::::::::::::::::::::::::::: solution

## Solution to Challenge 5

The variables of interest are gender (categorical, 2 groups), treatment 
(categorical, two groups) and dissolution time (continuous). The appropriate
test is therefore a two-way ANOVA
```r
# Visualisation
par(mfrow=c(1,2))
plot(Dis~Treatment+Gender, data=gallstones)

# Interaction plot to visualise
interaction.plot(gallstones$Treatment, gallstones$Gender, gallstones$Dis,
                 col=2:3,lwd=3,cex.axis=1.5,cex.lab=1.5)

# anova 2 way with aov function
result<-aov(Dis~Treatment+Gender+Treatment*Gender,data=gallstones)
summary(result)

# anova 2 way with lm function
result<-lm(Dis~Treatment+Gender+Treatment*Gender,data=gallstones)
anova(result)

# Checking the assumptions of anova

result<-lm(Dis~Treatment+Gender+Treatment*Gender,data=gallstones)
plot(result)
```
### Conclusion
With a p-value of <0.0016, there is evidence for a significant effect  of
treatment on dissolution time. There is no evidence for a difference between
genders for dissolution time, nor evidence for an interaction between
treatment and gender (both males and females have a similar response).

::::::::::::::::::::::::::::::::::::: 
::::::::::::::::::::::::::::::::::::: 

## Finishing up
It is always a good idea to conclude any R analysis with the `sessionInfo()`
command to save with the output as a permanent record of software versions being
used. This helps resolve issues where updated versions of tools may give 
different analysis outcomes.

```r
sessionInfo()
```

```{.output}
R version 4.2.2 Patched (2022-11-10 r83330)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: Ubuntu 22.04.1 LTS

Matrix products: default
BLAS:   /usr/lib/x86_64-linux-gnu/blas/libblas.so.3.10.0
LAPACK: /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3.10.0

locale:
 [1] LC_CTYPE=C.UTF-8       LC_NUMERIC=C           LC_TIME=C.UTF-8       
 [4] LC_COLLATE=C.UTF-8     LC_MONETARY=C.UTF-8    LC_MESSAGES=C.UTF-8   
 [7] LC_PAPER=C.UTF-8       LC_NAME=C              LC_ADDRESS=C          
[10] LC_TELEPHONE=C         LC_MEASUREMENT=C.UTF-8 LC_IDENTIFICATION=C   

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

other attached packages:
[1] FSA_0.9.3        ggplot2_3.4.0    gmodels_2.18.1.1

loaded via a namespace (and not attached):
 [1] knitr_1.41       magrittr_2.0.3   MASS_7.3-58.2    tidyselect_1.2.0
 [5] munsell_0.5.0    colorspace_2.1-0 R6_2.5.1         rlang_1.0.6     
 [9] fansi_1.0.4      dplyr_1.0.10     stringr_1.5.0    tools_4.2.2     
[13] grid_4.2.2       gtable_0.3.1     xfun_0.35        utf8_1.2.2      
[17] cli_3.4.1        withr_2.5.0      gtools_3.9.4     tibble_3.1.8    
[21] lifecycle_1.0.3  vctrs_0.5.1      glue_1.6.2       evaluate_0.18   
[25] gdata_2.18.0.1   stringi_1.7.8    compiler_4.2.2   pillar_1.8.1    
[29] generics_0.1.3   scales_1.2.1     renv_0.16.0      pkgconfig_2.0.3 
```

::::::::::::::::::::::::::::::::::::: keypoints

- Understand the impact of multiple testing on p-values
- Use `p.adjust` to correct multiple-testing p-values

::::::::::::::::::::::::::::::::::::: 

[r-markdown]: https://rmarkdown.rstudio.com/
