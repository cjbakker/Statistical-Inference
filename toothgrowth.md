Overview
--------

This project analyzes the Tooth Growth data available in the R datasets
package. It will provide a basic summary of the data, use confidence
intervals and/or hypothesis testing to compare tooth growth, and will
provide conclusions as well as the assumptions needed for those
conclusions. The data set includes sixty observations showing the tooth
length of 10 guinea pigs at each dosage of Vitamin C (0.5mg, 1mg, and
2mg) delivered either via orange juice or absorbic acid. Information on
labels was found here:
<https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/ToothGrowth.html>.

Exploratory Analysis
--------------------

First we load the data and take a look at it.

    data(ToothGrowth)
    str(ToothGrowth)

    ## 'data.frame':    60 obs. of  3 variables:
    ##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
    ##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...

So we can see that there are 60 observations of three variables: len,
supp, and dose. Len is the length of tooth, supp is the method of
delivery (VC is absorbic acid and OJ is orange juice), and dose is the
dosage of Vitamin C, either 0.5mg, 1mg, or 2mg. We can use the lattice
library to get an overview of the data using the aggregate function.

    library(lattice)
    attach(ToothGrowth)
    aggregate(len,list(dose,supp),FUN=function(x) c(x_mean=mean(x), x_sd=sd(x)))

    ##   Group.1 Group.2  x.x_mean    x.x_sd
    ## 1     0.5      OJ 13.230000  4.459709
    ## 2     1.0      OJ 22.700000  3.910953
    ## 3     2.0      OJ 26.060000  2.655058
    ## 4     0.5      VC  7.980000  2.746634
    ## 5     1.0      VC 16.770000  2.515309
    ## 6     2.0      VC 26.140000  4.797731

    detach(ToothGrowth)

We can also visualize some of this data in a box and whisper plot
showing us length compared to dose for both absorbic acid and orange
juice.

    ToothGrowth$dose <- as.factor(ToothGrowth$dose) #remember to convert dose to a factor before proceeding
    bwplot(len ~ dose | supp,data=ToothGrowth)

![](toothgrowth_files/figure-markdown_strict/unnamed-chunk-3-1.png)

Based on this visualization, it looks like for both absorbic acid and
orange juice, as the dose increases, the length of tooth increases.

Confidence Intervals and Hypothesis Testing
-------------------------------------------

We want to investigate whether dosage (dose) or method of delivery
(supp) are correlated to tooth length (len). We'll start by looking at
the method of delivery. Our null hypothesis is that there is no
difference in tooth length between the two methods.

    t.test(len ~ supp, paired=F, var.equal=F, data=ToothGrowth)

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  len by supp
    ## t = 1.9153, df = 55.309, p-value = 0.06063
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.1710156  7.5710156
    ## sample estimates:
    ## mean in group OJ mean in group VC 
    ##         20.66333         16.96333

We find a 95% confidence interval of -0.171, 7.571 for
mean(OJ)-mean(VC). Our p value is 0.06063, which is not significant.
Therefore, we would not reject the null hypothesis.

So now we move on to dosage. For this, we need to set up some data
frames to compare between the different dosages.

    dose.one <- subset(ToothGrowth, dose %in% c(0.5, 1.0))
    dose.two <- subset(ToothGrowth, dose %in% c(1.0, 2.0))
    dose.three <- subset(ToothGrowth, dose %in% c(0.5, 2.0))

Then we'll run our t tests comparing the different data frames (note: it
would be more appropriate to run an anova rather than multiple t tests,
but for the purposes of this assignment, we'll use the t tests). Our
null hypothesis is that there is no difference in tooth length between
the different dosages.

    t.test(len ~ dose, paired=F, var.equal=F, data=dose.one)

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  len by dose
    ## t = -6.4766, df = 37.986, p-value = 1.268e-07
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -11.983781  -6.276219
    ## sample estimates:
    ## mean in group 0.5   mean in group 1 
    ##            10.605            19.735

We find a 95% confidence interval of -11.98, -6.27 for
mean(0.5)-mean(1.0) with a p value of 1.268e-07. Therefore, we reject
the null hypothesis.

    t.test(len ~ dose, paired=F, var.equal=F, data=dose.two)

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  len by dose
    ## t = -4.9005, df = 37.101, p-value = 1.906e-05
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -8.996481 -3.733519
    ## sample estimates:
    ## mean in group 1 mean in group 2 
    ##          19.735          26.100

We find a 95% confidence interval of -8.99, -3.73 for
mean(1.0)-mean(2.0) with a p value of 1.906e-05. Therefore, we reject
the null hypothesis.

    t.test(len ~ dose, paired=F, var.equal=F, data=dose.three)

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  len by dose
    ## t = -11.799, df = 36.883, p-value = 4.398e-14
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -18.15617 -12.83383
    ## sample estimates:
    ## mean in group 0.5   mean in group 2 
    ##            10.605            26.100

We find a 95% confidence interval of -18.15, -12.83 for
mean(0.5)-mean(2.0) with a p value of 4.398e-14. Therefore, we reject
the null hypothesis.

Conclusion and Assumptions
--------------------------

We have found that method of delivery does not result in a difference in
tooth length, but that the dose does result in a difference in tooth
length. This analysis assumes that these are independent populations,
meaning that there must be a minimum of 60 guinea pigs being observed
and no guinea pigs have been assigned to multiple groups. We are
assuming that the guinea pigs represent an appropriate sample and that
researchers taking these measurements were blinded to the dosages and
method of delivery. We assume that there is no conflict of interest
among the researchers.
