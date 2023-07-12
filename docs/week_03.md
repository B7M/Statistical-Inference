

# Intervals, Testing, & Pvalues

When we estimate something using statistics, usually that estimate comes with uncertainty. Take, for example, election polling. When we get a polled percentage of voters that favor a candidate, we were only able to sample a small subset of voters. Therefore, our estimate has uncertainty associated with it. Confidence intervals are a convenient way to communicate that uncertainty in estimates. In this lecture, we will discuss confidence intervals. We will also discuss the concept of hypothesis testing and p-values.

## Confidence intervals

In the previous section, we explored the creation of confidence intervals using the central limit theorem. The intervals we discussed followed:
$$ \text{Est} \pm \text{ZQ} \times {SE_{Est}} $$
where the estimates are plus or minus a quantile from the standard normal distribution multiplied by the estimated standard error.
Here, we will focus on methods suitable for small sample sizes. Specifically, we will discuss the Student's or Gosset's T distribution and T confidence intervals. 
$$ \text{Est} \pm \text{TQ} \times {SE_{Est}} $$
where the estimates are plus or minus a quantile from the T quantile multiplied by the estimated standard error. The only difference is that we have replaced the Z quantile with a T quantile.

The T distribution has heavier tails compared to the normal distribution, resulting in slightly wider intervals. These intervals are extremely useful in statistics, and when you have the option to choose between a T interval and a Z interval for cases where both are available, it is advisable to select the T interval. As you gather more data, the T interval gradually becomes more similar to the Z interval. We will cover the single and two-group versions of the T interval. Additional T intervals that are valuable will be discussed in our regression class. The T distribution was developed by William Gosset, who published his work under the pseudonym "Student" in 1908. Since Gosset worked for the Guinness Brewery, they did not allow him to publish under his real name.

Unlike the normal distribution, which is characterized by two parameters (mean,variance), the T distribution is primarily centered around __zero__ with a standardized formula for the scale. It is indexed by a single parameter known as degrees of freedom. As the degrees of freedom increase, the T distribution becomes more similar to the standard normal distribution. The reason for the T distribution is that when we divide the difference between the sample mean ($\bar X$) and the population mean by the estimated standard error for independent and identically distributed (iid) Gaussian data, the resulting distribution is not Gaussian. 
$$ \frac{\bar X - \mu}{S/\sqrt{n}} $$
If we replaced the estimated standard error ($S$) with the true standard deviation ($\sigma$), the distribution would be exactly standard normal. However, when using the estimated standard error, the distribution follows a T distribution with $n-1$ degrees of freedom. This distinction becomes less significant as the sample size ($n$) increases, but for small sample sizes, the difference can be substantial. Using the standard normal distribution for small sample sizes can lead to narrow confidence intervals.
The formula for the T interval:
$$ \bar X \pm T_{n-1} \times \frac{S}{\sqrt{n}} $$
The following graph shows t distribution overlaid the normal distribution for various degrees of freedom using R Studios `manipulate` function.

![(\#fig:unnamed-chunk-1)T distribution overlaid on normal distribution](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_128.png)


```r
k <- 1000
xvals <- seq(-5, 5, length = k)
myplot <- function(df){
  d <- data.frame(y = c(dnorm(xvals), dt(xvals, df)),
                  x = xvals,
                  dist = factor(rep(c("Normal", "T"), c(k,k))))
  g <- ggplot(d, aes(x = x, y = y)) 
  g <- g + geom_line(size = 2, aes(colour = dist))
  g
}
manipulate(myplot(mu), mu = slider(1, 20, step = 1))  
```

As the degrees of freedom decrease, the T distribution exhibits heavier tails compared to the normal distribution. However, in the plot, it may be challenging to observe the impact clearly as the focus is on the peak region where the distributions are similar. To illustrate the quantiles, we also plotted the t distribution quantiles against the normal distribution quantiles, starting at the 50th percentile. 

![(\#fig:unnamed-chunk-3)T distribution quantiles overlaid on normal distribution quantiles](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_131.png)


```r
pvals <- seq(.5, .99, by = .01)
myplot2 <- function(df){
  d <- data.frame(n= qnorm(pvals),t=qt(pvals, df),
                  p = pvals)
  g <- ggplot(d, aes(x= n, y = t))
  g <- g + geom_abline(size = 2, col = "lightblue")
  g <- g + geom_line(size = 2, col = "black")
  g <- g + geom_vline(xintercept = qnorm(0.975))
  g <- g + geom_hline(yintercept = qt(0.975, df))
  g
}
manipulate(myplot2(df), df = slider(1, 20, step = 1))
```

The plot includes reference lines for the 97.5th quantile, which is typically around 1.96 for the standard normal distribution but can be considerably larger for the T distribution. For instance, with two degrees of freedom, the T quantile exceeds four. However, having only three data points to estimate the variance (n-1 degrees of freedom) is not ideal. When we increase the degrees of freedom to 20, the T quantiles become much closer to the normal quantiles. The light blue reference line represents the identity line, and deviations from this line demonstrate the distinction between the two intervals. The T interval will yield a quantile slightly larger than two, while the Z interval will yield a quantile slightly smaller than two. This discrepancy can have a notable impact on the intervals and even determine whether the interval includes zero or not. Hence, we opt for the T distribution in such cases.

In summary, the T interval is wider than the normal interval due to the additional parameter we estimate, the standard deviation. The T interval assumes the data are independent and identically distributed (iid) and approximately symmetric and mound-shaped. It is also applicable for paired observations, such as measurements taken at different times on the same units, by considering the differences or differences on the logarithmic scale. As the degrees of freedom increase, the T quantiles approach those of the standard normal distribution. Therefore, it is advisable to use the T interval rather than selecting between the T and normal intervals. For skewed distributions, the assumptions of the T interval are violated, and alternative procedures like working on the log scale or using bootstrap confidence intervals may be more appropriate. Lastly, for highly discrete data, such as binary or Poisson data, other intervals are available and preferable to the T interval.

In R, if you type `data(sleep)`, it will load the sleep data set, which was originally analyzed in Gosset's Biometrika paper. The data set shows the increase in hours slept for patients on sleep medications. R treats the data as two groups, but we will treat it as paired data. To load the data, you can use the command `head(sleep)` to view the first few rows of the data frame. The variable `extra` represents the extra hours slept, `group` is the group ID, and `ID` is the subject ID. The subjects are numbered from 1 to 10, and then the numbering repeats.

![(\#fig:unnamed-chunk-5)Sleep data](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_134.png)


```r
library(ggplot2)
g <- ggplot(sleep, aes(x = group, y = extra, group = factor(ID)))
g <- g + geom_line(size = 1, aes(colour = ID)) + geom_point(size =10, pch = 21, fill = "salmon", alpha = .5)
g
```

This visualization clearly demonstrates the benefit of acknowledging that these are repeat measurements on the same subjects. If you fail to acknowledge this, you would be comparing the variation within group 1 to the variation within group 2. However, if you acknowledge the pairing, you compare the subject-specific differences across groups, where the variation in these differences is lower due to the correlation within subjects.


```r
g1 <- sleep$extra[1 : 10]; g2 <- sleep$extra[11 : 20]
difference <- g2 - g1
mn <- mean(difference); s <- sd(difference); n <- 10
```

To calculate the differences between group 2 and group 1, we extract the first ten measurements (subjects 1-10) and the latter ten measurements (subjects 1-10 on the second medication). The vector `y_subdra` represents the subtraction of group 2 minus group 1, and we calculate the mean and standard deviation of the difference. To obtain a t confidence interval, we use the formula and define n as 10. Alternatively, you can use the function `t.test` by passing it the two vectors and setting the argument `paired` to true. Another option is to use a model statement, such as `outcome ~ group, paired = TRUE, data = sleep`.


```r
mn + c(-1, 1) * qt(.975, n-1) * s / sqrt(n)
t.test(difference)
t.test(g2, g1, paired = TRUE)
t.test(extra ~ I(relevel(group, 2)), paired = TRUE, data = sleep)
```


```r
rbind(
mn + c(-1, 1) * qt(.975, n-1) * s / sqrt(n),
as.vector(t.test(difference)$conf.int),
as.vector(t.test(g2, g1, paired = TRUE)$conf.int),
as.vector(t.test(extra ~ I(relevel(group, 2)), paired = TRUE, data = sleep)$conf.int)
)
```

We formatted the results into a matrix for better readability. The output provides similar results from these commands, indicating that the difference in means between the groups is between 0.7 and 2.46. Since this is a confidence interval, we can interpret it as follows: if we were to repeatedly perform this procedure on independent samples, about 95% of the intervals obtained would contain the true mean difference we are estimating. This assumes that the subjects are a relevant sample from the population of interest.

### Independent group T intervals

Suppose we want to compare the mean blood pressure between two groups in a randomized trial: the treatment group and the placebo group. This scenario is similar to A/B testing, commonly used in data science. In both A/B testing and randomized trials, randomization is performed to balance unobserved covariates that may affect the results. Since randomization has been conducted, it is reasonable to compare the two groups using a t confidence interval or a t test. However, we cannot use a paired t test in this case because there is no matching of subjects between the two groups. Therefore, we will discuss methods for comparing independent groups.

The standard confidence interval for comparing independent groups is calculated as follows: 
$$ \bar X_1 - \bar X_2 \pm t_{n_1 + n_2 - 2,1-\alpha/2} \times S_p\sqrt{\frac{1}{n_1} + \frac{1}{n_2}} $$

The degrees of freedom (df) are determined by $n_1 + n_2 - 2$, where $n_1$ is the number of observations in group $X_1$ and $n_2$ is the number of observations in group $X_2$. The standard error of the difference is given by $S_p \times \sqrt{1/n_1 + 1/n_2}$, where $S_p$ is the pooled standard deviation.

The pooled standard deviation $S_p$ is the square root of the pooled variance. It is an estimate of the common variance if we assume that the variances in the two groups are equal due to randomization. The pooled variance is a weighted average of the variances from each group, with the weights determined by the sample sizes. If the sample sizes are equal, the pooled variance is the simple average of the variances.
$$ S_p^2 = \frac{(n_1 - 1)S_1^2 + (n_2 - 1)S_2^2}{n_1 + n_2 - 2} $$
Note: This interval assumes a constant variance across the two groups. If this assumption is violated, the interval may not provide accurate coverage. In such cases, alternative approaches accounting for different variances per group should be considered.

Here is an example from Rosner's "Fundamentals of Biostatistics" book, we compare 8 oral contraceptive users to 21 controls regarding blood pressure. The average systolic blood pressure for contraceptive users is 133 mmHg with a standard deviation of 15, while the control group has an average blood pressure of 127 mmHg with a standard deviation of 18.

To manually construct the independent group interval, we calculate the pooled standard deviation by taking the square root of the weighted average of the variances. The weights are determined by the sample sizes and adjusted for degrees of freedom. We then compute the interval. In this specific example, the interval ranges from negative 10 to 20. Since the interval contains zero, we cannot rule out the possibility of the population difference being zero.


```r
sp <- sqrt((7 * 15.34^2 + 20 * 18.23^2) / (8 + 21 - 2))
132.86 - 127.44 + c(-1, 1) * qt(.975, 27) * sp * (1 / 8 + 1 / 21)^.5
```

Aa another example we'll revisit the sleep patients example, but this time let's assume that the subjects were not matched. In this case, we have $n_1$ and $n_2$ as the sample sizes for group 1 and group 2, respectively. Both of these sample sizes will be 10 in this example.

We begin by constructing the pooled standard deviation estimate, calculating the mean difference, and determining the standard error of the mean difference. Then, we manually construct the confidence interval. Next, we use the `t.test` function to perform the t-test, specifying `paired` equals FALSE to indicate that the samples are not paired, and `var.equal` equals TRUE to assume equal variances in the two groups. We extract the confidence interval from the `t.test` results.


```r
n1 <- length(g1); n2 <- length(g2)
sp <- sqrt( ((n1 - 1) * sd(g1)^2 + (n2-1) * sd(g2)^2) / (n1 + n2-2))
md <- mean(g2) - mean(g1)
semd <- sp * sqrt(1 / n1 + 1/n2)
rbind(
md + c(-1, 1) * qt(.975, n1 + n2 - 2) * semd,  
t.test(g2, g1, paired = FALSE, var.equal = TRUE)$conf,
t.test(g2, g1, paired = TRUE)$conf
)
```

Comparing the results of the manual calculation and the t.test, we find that they agree perfectly. However, the interval obtained when considering the pairing of subjects is entirely above 0, whereas the interval obtained without considering pairing contains 0. The plot of the data clearly illustrates why this is the case. When comparing the variability between the two groups, there is significant variability. However, when accounting for pairing and considering the variability in the differences within each subject, a substantial portion of the variability is explained by inter-subject differences.

Example: We will use the `ChickWeight` dataset in R, which contains weight measurements of chicks from birth to a few weeks later. To access the dataset, you can load the `datasets` package and use the command `data(ChickWeight)`. To work with the data, the `reshape2` package is recommended.

The ChickWeight data is initially in a long format, where the chicks are arranged in a long vector. If you want to convert it to a wide format, where each time point has its own column, you can use the `dcast` function from the reshape2 package. By applying `dcast` to the `ChickWeight` data frame, with `Diet` and `Chick` as the variables that remain the same, and `Time` as the variable to be converted from long to wide format, you can reshape the data. If you prefer different column names, you can rename them accordingly.


```r
library(datasets); data(ChickWeight); library(reshape2)
##define weight gain or loss
wideCW <- dcast(ChickWeight, Diet + Chick ~ Time, value.var = "weight")
names(wideCW)[-(1 : 2)] <- paste("time", names(wideCW)[-(1 : 2)], sep = "")
library(dplyr)
wideCW <- mutate(wideCW,
  gain = time21 - time0
)
```

Furthermore, you may want to create a specific variable that represents the total weight gain from time zero in the dataset. We utilized the `dplyr` package for data manipulation. First, we used the `mutate` command to create a new variable in the data frame. This variable represents the change in weight, calculated as the final time point weight minus the baseline weight. From this point onward, we will analyze the change in weight variable. Before conducting the test, let's examine the data visually. 

![(\#fig:unnamed-chunk-13)ChickWeight data](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_137.png)


```r
g <- ggplot(ChickWeight, aes(x = Time, y = weight, 
                             colour = Diet, group = Chick))
g <- g + geom_line()
g <- g + stat_summary(aes(group = 1), geom = "line", fun.y = mean, size = 1, col = "black")
g <- g + facet_grid(. ~ Diet)
g
```

We created a spaghetti plot using the `ggplot2` package, which displays the weight measurements for each of the four diets over time. Each line represents a different diet, starting from the baseline and ending at the final time point. It appears that there are some noticeable differences, particularly regarding the variability between the diets. However, due to varying sample sizes, it can be challenging to make definitive conclusions. We included a reference line representing the mean for each diet. Without conducting a formal statistical test, it seems that the average weight gain for the first diet is slightly slower than that of the fourth diet. To confirm this observation, proceed with a formal confidence interval analysis. Instead of plotting individual measurements, we created a violin plot to compare the end weight minus the baseline weight for diets one and four. We will be comparing these two violin plots. The assumption of equal variances seems questionable in this case. To perform a t-test, we need the explanatory variable to have only two levels. To address this, we used the `subset` command to filter the data, including only records where the diet variable is one or four, excluding diets two and three. Please note that if you conduct this analysis on your own, you may want to compare all possible combinations (e.g., one to two, one to three, one to four, etc.), and adjust for multiplicity if necessary.

![(\#fig:unnamed-chunk-15)ChickWeight data](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_160.png)


```r
g <- ggplot(wideCW, aes(x = factor(Diet), y = gain, fill = factor(Diet)))
g <- g + geom_violin(col = "black", size = 2)
g
```

Next, we used the `t.test` function to calculate the confidence interval. Since the vectors for diet one and diet four have different lengths, the paired equals TRUE option is not available. We compared the assumption of equal variances versus the assumption of unequal variances. The resulting intervals differ, but both indicate that weight gain on diet one is lower than on diet four. The first interval is -108 to -14, and the second is -104 to -18, with both intervals entirely below zero. However, whether the specific interval change is substantial or not depends on the dataset, which we don't have enough information about.


```r
wideCW14 <- subset(wideCW, Diet %in% c(1, 4))
rbind(
t.test(gain ~ Diet, paired = FALSE, var.equal = TRUE, data = wideCW14)$conf,
t.test(gain ~ Diet, paired = FALSE, var.equal = FALSE, data = wideCW14)$conf
)
```

### A note on unequal variance

Hopefully the formula for the case of unequal variances seems familiar to you. 
$$ \bar X_1 - \bar X_2 \pm t_{df} \times \sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}} $$

It involves calculating the difference in means and adding or subtracting a t quantile times the standard error. The standard error is calculated assuming different variances in each of the two groups. However, it's important to note that if the $X_1$ and $X_2$ observations are independent and identically distributed (iid) normal, potentially with different means and variances, the relevant normalized statistic does not follow a t distribution exactly. Instead, it can be approximated by a t distribution with a specific formula for degrees of freedom. 
$$ df = \frac{\left({S_1^2}/{n_1} + {S_2^2}/{n_2}\right)^2}{({\frac{S_1^2}{n_1}})^2/{(n_1 - 1)} + {({\frac{S_2^2}{n_2}})^2}{(n_2 - 1)}} $$
While the degrees of freedom calculation may seem unusual because it doesn't involve sample sizes, it relies on estimated standard deviations and variances from the two groups. Despite this complexity, using this approach yields a t calculation that closely approximates the true distribution, even though it's not strictly a t distribution. In practice, it's often recommended to use the unequal variance interval when in doubt.

We demonstrate the calculations for the oral contraceptive example mentioned earlier. Going through this calculation can help you understand how to plug in the values, particularly noting that the degrees of freedom in this case are 15.04. However, typically, when we want to perform unequal variance t tests, we can simply use the `t.test` function in R with `var.equal` set to FALSE. This will conduct the relevant t test with unequal variances and provide the corresponding t quantile.

$$132.86 - 127.44 \pm 2.13 \left(\frac{15.34^2}{8} + \frac{18.23^2}{21} \right)^{1/2}= [-8.91, 19.75]$$

In R, `t.test(..., var.equal = FALSE)`

In summary we explored creating intervals using the t distribution, which are highly useful in statistics. When dealing with single or paired observations, where the differences are taken into account, the t interval provides robust intervals that are not heavily dependent on assumptions about the data distribution. However, there are cases where alternatives to the t distribution and t intervals may be preferable. For example, if the data is highly skewed, it may be beneficial to consider taking a logarithmic transformation or explore different procedures. In addition, for binary data, odds ratios can be more suitable, which we will cover in the regression class's generalized linear model component. Similar considerations apply to count data, where we will discuss Poisson models and generalized linear models for rates in the regression class. For other special cases involving two groups, you can find further coverage in the course "Mathematical Biostatistics Boot Camp 2" on Coursera.

## Hypothesis testing
Deciding between two hypotheses is a core activity in scientific discovery. Statistical hypothesis testing is the formal inferential framework around choosing between hypotheses. After covering confidence intervals, developing an understanding of hypothesis testing should be relatively straightforward.

Hypothesis testing involves making decisions based on data. It typically begins with a null hypothesis, denoted as $H_0$, which represents the status quo or a default assumption. The null hypothesis is assumed to be true initially, and we need statistical evidence to reject it in favor of an alternative hypothesis $H_a$, often referred to as the research hypothesis. 

Suppose we are interested in studying sleep disordered breathing, and a respiratory disturbance index (RDI) of more than 30 events per hour indicates severe sleep disordered breathing. In a sample of 100 overweight subjects with other risk factors for sleep disordered breathing in a sleep clinic, we find that the mean RDI is 32 events per hour, with a standard deviation of 10 events per hour. We want to test whether the population mean RDI for this population is equal to 30 (our benchmark for severe sleep disordered breathing) or if it is greater than 30. We can specify the null hypothesis as $H_0: μ = 30$ and the alternative hypothesis as $H_a: μ > 30$. In this example, we are interested in determining if the respiratory disturbance index for this population is greater than 30.

Please note that the truth can only be one of the following: either $H_0$ is true, or $H_a$ is true. As a result, there are only four possible outcomes. If the truth is $H_0$, and we decide to accept $H_0$, we have correctly accepted the null hypothesis. If the truth is $H_0$, but we decide to reject $H_0$ and accept $H_a$, we have made a **Type I error**. In the hypothesis testing framework we will present, we aim to control the probability of Type I error to be small. On the other hand, if the truth is $H_a$, and we correctly reject $H_0$ and accept $H_a$, we have correctly rejected the null hypothesis. However, if the truth is $H_a$, but we mistakenly accept $H_0$ and reject $H_a$, we have made a **Type II error**. _The rates of Type I and Type II errors are related, meaning that as the Type I error rate decreases, the Type II error rate tends to increase, and vice versa._

Truth | Decide | Result |
---|---|---|
$H_0$ | $H_0$ | Correctly accept null |
$H_0$ | $H_a$ | Type I error |
$H_a$ | $H_a$ | Correctly reject null |
$H_a$ | $H_0$ | Type II error |

An analogy that can help illustrate this is a court of law. In most courts, the null hypothesis is that the defendant is innocent until proven guilty. Rejecting the null hypothesis in this case would mean convicting the defendant. We require evidence and set a standard for that evidence to reject the null hypothesis and convict someone. If we set a very low standard, meaning we don't require much evidence to convict, we may increase the percentage of innocent people wrongly convicted (Type I errors). However, we would also increase the percentage of guilty people correctly convicted. On the other hand, if we set a very high standard, such as requiring irrefutable evidence to convict, we may increase the percentage of innocent people correctly acquitted (a good thing), but we would also increase the percentage of guilty people wrongly acquitted (Type II errors). This example demonstrates the relationship between Type I and Type II error rates.

Ideally, we aim to gather better evidence for a given standard, such as increasing the sample size.

Let's revisit the respiratory disturbance index example and consider a reasonable testing strategy. We can reject the null hypothesis if our sample mean respiratory disturbance index is larger than a certain constant, denoted as $C$. This constant takes into account the variability of the sample mean $\bar X$. Typically, $C$ is chosen such that the probability of a Type I error (rejecting the null when it is true) is low. In hypothesis testing, a common benchmark for the Type I error rate is 5%.

To determine the appropriate constant $C$, we need to consider the standard error of the mean, which is 10 (assumed standard deviation of the population) divided by the square root of the sample size, which is 100 in this case, resulting in a value of 1. 
$$ \frac{10}{\sqrt{100}} = 1 $$
Under the null hypothesis $H_0: \bar X \sim N(30,1)$, the distribution of the sample mean $\bar X$ follows a normal distribution with a mean of 30 and a variance of 1 (calculated from the standard error squared).

We want to choose the constant $C$ such that $P(\bar X>C;H_0)$ is 5%. The 95th percentile of the standard normal distribution corresponds to 1.645 standard deviations from the mean. Therefore, setting $C$ as 1.645 standard deviations from the mean under the null hypothesis will achieve the desired probability of 5%. In this case: $$C=30+ 1×1.645 = 31.645$$

To summarize, the rule is to reject the null hypothesis if the observed sample mean is larger than 31.645. This rule ensures that we will reject the null hypothesis 5% of the time when the null hypothesis is true, assuming a sample size of 100 and a population standard deviation of 10. Instead of calculating $C$ in the original units of the data, it is common to convert the sample mean into standard error units from the hypothesized mean. For example, if the observed sample mean is 32, the hypothesized mean is 30, and the standard error is 2, which is greater than 1.645, the chance of this occurring is less than 5%. Therefore, we would reject the null hypothesis in favor of the alternative hypothesis.

To summarize the rule, we reject the null hypothesis when (X bar - hypothesized mean) divided by the standard error of the mean is greater than the appropriate upper quantile that leaves Alpha percent in the upper tail.
$$ \frac{\bar X - \mu_0}{\sigma/\sqrt{n}} > z_{1-\alpha} $$

### T tests

Suppose the sample size $n$ is now 16 instead of 100. The test statistic remains the same, which is calculated as the sample mean minus the hypothesized mean $H_0: μ = 30$ divided by the standard error of the mean. However, now we have a square root of 16 instead of the square root of 100, and the standard deviation $s$ is still 10.
$$ \frac{\bar X - 30}{s/\sqrt{16}} = 0.8 $$
In this case, the test statistic follows a t-distribution with 15 degrees of freedom. Under the null hypothesis, the probability of the test statistic being larger than the $95^{th}$ percentile of the T-distribution is 5%. To calculate this percentile, we can use the function `qt(0.95, 15)`, which gives a value of 1.7531. If we plug in the values of s = 10 and x bar = 32 into the test statistic formula, we get a test statistic value of 0.8. Since 0.8 is smaller than 1.7531, we fail to reject the null hypothesis.
For two-sided test, we want to reject the null hypothesis if the mean is different from 30, regardless of whether it is too large or too small. To achieve a two-sided test, we need to split the probability equally in both tails of the distribution, resulting in a 2.5% probability in each tail.

We can calculate the critical values for the two-sided test by using the function `qt(0.975, 15)` and `qt(0.025, 15)`. These values represent the 2.5th percentile and 97.5th percentile of the T-distribution with 15 degrees of freedom. If the test statistic is larger than the positive critical value or smaller than the negative critical value, we reject the null hypothesis. Alternatively, we can take the absolute value of the test statistic and reject it if it is larger than the positive critical value. In our example, since the test statistic is positive, we only need to consider the larger side. Since 0.8 is smaller than the positive critical value $2.5^{th} percentile$, we fail to reject the null hypothesis in the two-sided test. In practice, instead of manually calculating the rejection region and performing hypothesis tests, it is common to use statistical functions like `t.test()` in R. These functions provide all the relevant statistics, including the test statistic, degrees of freedom, and p-values. They also offer the option to perform paired tests when appropriate.

As an illustration, we can use the `t.test()` function in R with the `father.son` dataset to test whether the population mean of son's heights is equivalent to the population mean of father's heights. 


```r
library(UsingR); data(father.son)
t.test(father.son$sheight - father.son$fheight)
```

Since the observations are paired, we can pass the difference between son's and father's heights directly to the function or specify `paired = TRUE` when passing the individual vectors. The `t.test()` function provides the t statistic, degrees of freedom, and automatically calculates the t confidence interval. By analyzing the output, we can determine the statistical significance and evaluate the practical significance of the results by examining the range of values in the confidence interval.
### Confidence intervals and hypothesis tests

The `t.test()` function conveniently provides the t confidence interval automatically. It is valuable to examine the confidence interval alongside the test output as it helps bridge the gap between statistical significance and practical significance. By assessing the range of values in the confidence interval, which is expressed in the units of the data of interest, we can determine whether they are practically meaningful or not. In the previous section in the book on confidence intervals, we explored whether a hypothesized mean was supported by checking if it fell within the confidence interval. Similarly, we performed a hypothesis test to determine if the mean was equal to a specific value or not. **It turns out that these two procedures do not disagree.** Checking whether the hypothesized mean $μ_0$ falls within the interval is equivalent to conducting a two-sided hypothesis test, with the caveat that the significance level $α$ used for the interval should be equal to $1-α$ where $\alpha$ is the significance level used for the hypothesis test. In other words, if we construct a 95% confidence interval and check whether $μ0$ is within that interval, we fail to reject the null hypothesis if it is inside the interval and reject it if it is outside. This procedure aligns with performing the hypothesis test at a significance level of $α$. This relationship is stated in the slide, where the confidence interval can be seen as the set of all possible values for which we fail to reject the null hypothesis.

With the understanding of confidence intervals and hypothesis tests for one group in place, extending these concepts to two groups is a straightforward extension. The rejection rules remain the same, and now we want to test whether the means of two groups are equal or not. We have the same set of alternatives: $μ1 > μ2$, $μ1 < μ2$, or $μ1 ≠ μ2$. The test statistic remains the same, calculated as the difference between the sample means $\bar X_1 - \bar X_2$ minus the hypothesized mean difference $μ_1 - μ_2$, divided by the standard error of the mean.

Example: Take the `ChickWeight` dataset. 
We can load the dataset using `library(datasets)` and `data(ChickWeight)`. To reshape the data into the desired format, we may need to use the `reshape2` package. The `ChickWeight` dataset contains measurements for different chicks at various time points, represented in a long format. We desired a wide format for our data, so we used the `dcast()` function to achieve that. Additionally, we renamed the resulting dataset and defined a new variable called `weight_gain`, which represents the difference between `time21` and `time0`. In this particular dataset, most chicks had nearly identical weights at `time0` relative to `time21`. Although this doesn't significantly affect the results, we wanted to demonstrate the use of the `mutate()` function, which simplifies adding variables to a data frame.

To conduct the t-test, we selected a subset of the data where the diet was either 1 or 4. This was necessary because the tilde operator in the `t.test()` function requires the predictor variable `Diet` to have exactly two levels when comparing groups. By setting `paired = FALSE`, we indicate that the chicks receiving diet 1 and diet 4 are completely separate groups, and there is no pairing between them. Chick 1 from diet 1 has no connection to chick 1 from diet 4.


```r
library(datasets); data(ChickWeight); library(reshape2)
##define weight gain or loss
wideCW <- dcast(ChickWeight, Diet + Chick ~ Time, value.var = "weight")
names(wideCW)[-(1 : 2)] <- paste("time", names(wideCW)[-(1 : 2)], sep = "")
library(dplyr)
wideCW <- mutate(wideCW,
  gain = time21 - time0
)
```

We discussed how assuming equal variances may not be the best approach for this dataset. Therefore, we suggest trying the example with `var.equal = FALSE` to observe how the results change.


```r
wideCW14 <- subset(wideCW, Diet %in% c(1, 4))
t.test(gain ~ Diet, paired = FALSE, 
       var.equal = TRUE, data = wideCW14)
```



The resulting t statistic represents the estimate of the difference in average weight gain between the two diets, minus the hypothesized value of 0. When comparing two groups, unless a specific hypothesized difference in means is specified, the default assumption is that we are testing whether the means are equal under the null hypothesis or different under the alternative. The degrees of freedom are calculated as $n_1 + n_2 - 2$, which we covered in the confidence interval section. If unequal variances are used, fractional degrees of freedom may be obtained. The output also provides the confidence interval, which is always useful to examine when performing a hypothesis test. In the following section, we will discuss the concept of p-values and how they facilitate hypothesis testing. The calculated T statistic of -2.7 indicates how many estimated standard errors the difference in means is from the hypothesized mean. Since it is far into the tail of the t-distribution or normal distribution, it falls well below our cutoff value. Although we don't explicitly determine a cutoff value in this case, we can immediately conclude, after learning about p-values, that this result would be rejected in a 5% level test without calculating the specific t quantiles.

Example: Suppose you have a friend who has eight children, with seven of them being girls. You want to evaluate whether this supports the belief that the genders are independent and equally likely (like a fair coin flip). You want to test the null hypothesis that the probability of having a girl is 0.5 against the alternative hypothesis that it is greater than 0.5 ($H_0: p = 0.5$ vs. $H_a: p > 0.5$), as you are slightly skeptical.

To determine the number of girls the couple could have for the probability of having that many or more to be less than 5% under the null hypothesis of a fair coin, we can set up a rejection region. However, if we consider a rejection region from zero to eight girls, we would always reject the null hypothesis.

Rejection region | Type I error rate |
---|---|
[0 : 8] | 1
[1 : 8] | 0.9961
[2 : 8] | 0.9648
[3 : 8] | 0.8555
[4 : 8] | 0.6367
[5 : 8] | 0.3633
[6 : 8] | 0.1445
[7 : 8] | 0.0352
[8 : 8] | 0.0039

If we set up a rejection region where we would reject the null hypothesis if the couple had one to eight girls, we still wouldn't achieve a 5% significance level. It would be nearly one, indicating a very high rejection rate. However, if we consider having seven or eight girls as the rejection region, the probability of rejecting under the null hypothesis is just under 5%. It is worth noting that we cannot achieve an exact 5% level test in this case due to the discrete nature of the binomial distribution. For larger sample sizes, a normal approximation could have been used by treating the coin flip outcomes as averages and assuming a Gaussian distribution. However, you already know how to handle that. In this specific test, we observe that the closest rejection region consists of having seven or eight girls. Since your friend had seven girls, we would reject the null hypothesis based on this observation. However, it's important to acknowledge that an exact 5% level test is not feasible in this case due to the discrete nature of the binomial distribution. For two-sided tests, the approach is not obvious. We will discuss a method for conducting two-sided tests in the next lecture, and we believe it will become clearer when we introduce the concept of p-values. If this example seems confusing, the lecture on p-values will help clarify it. The exact binomial or Poisson tests will become easier to comprehend.

If you can perform a two-sided test for a binomial or Poisson distribution, you can also invert those tests. By considering the values for which you would fail to reject the null hypothesis, you can generate exact confidence intervals for the binomial and Poisson parameters. This is precisely how R calculates exact binomial intervals, without relying on asymptotic or central limit theorem approximations. They invert a two-sided hypothesis test of this nature.

## P values

P-values are a convenient way to communicate the results of a hypothesis test. When communicating a P-value, the reader can perform the test at whatever Type I error rate that they would like. Just compare the P-value to the desired Type I error rate and if the P-value is smaller, reject the null hypothesis. Formally, the P-value is the probability of getting data as or more extreme than the observed data in favor of the alternative. The probability calculation is done assuming that the null is true. In other words if we get a very large T statistic the P-value answers the question "How likely would it be to get a statistic this large or larger if the null was actually true?". If the answer to that question is "very unlikely", in other words the P-value is very small, then it sheds doubt on the null being true, since you actually observed a statistic that extreme. P-values are widely used as a measure of statistical significance. Almost every statistical software that performs hypothesis tests provides a p-value as an output. However, due to their popularity and frequent misinterpretation, p-values have become a subject of controversy among statisticians. Our main goal is to understand how to generate p-values correctly and interpret them appropriately.

The fundamental concept of a p-value is to start by assuming the null hypothesis, which assumes no effect or relationship, and then calculate the probability of obtaining evidence as extreme or more extreme than the evidence observed under this null hypothesis. In other words, we assess how unusual our observed result is if the null hypothesis were true. First we will follow a simple three-step approach, and then we will delve into the calculations.

Firstly, we establish the hypothetical distribution of a summary statistic, often referred to as a test statistic, such as the t-statistic from the t-test lecture, assuming no effect or relationship (null distribution). Secondly, we calculate the test statistic using the actual data we have. For example, in the case of a t-test, we substitute the empirical mean, subtract the hypothesized mean, and divide by the standard error. Finally, we determine the probability of obtaining a test statistic as extreme or more extreme than the one calculated. In other words, we compare our calculated test statistic to the hypothetical distribution and assess its level of extremity towards the alternative hypothesis. If the p-value is small, it indicates that the probability of observing a test statistic as extreme as the one we observed is low under the assumption that the null hypothesis is true.

Formally, the p-value is the probability, under the null hypothesis, of obtaining evidence as extreme or more extreme than what was actually observed. Typically, this evidence refers to the test statistic. Therefore, the p-value represents the probability of obtaining a test statistic as extreme or more extreme in favor of the alternative hypothesis than the observed test statistic. If the p-value is small, it suggests that either the null hypothesis is true, and we have observed something highly supportive of the alternative that is unlikely to occur, or the null hypothesis itself is false.

Example: Suppose we want to test the null hypothesis $\mu = \mu_0$ versus the alternative hypothesis $\mu > \mu_0$. If our calculated t-statistic is 2.5 with 15 degrees of freedom, we can determine the probability of obtaining a t-statistic as large as 2.5 in this scenario. By calculating `pt(2.5, 15, lower.tail = false)`, we find that the probability is approximately 1%. Therefore, the probability of observing evidence as extreme or more extreme than what was actually obtained under the null hypothesis is 1%. This suggests that either the null hypothesis is true and we have observed an unusually large test statistic, or the null hypothesis is false. Another way to interpret the p-value is as the attained significance level.

Example: Imagine our test statistic is 2 for the null hypothesis $\mu =30$ versus the alternative hypothesis $\mu >30$. Test statistics larger than 2 provide stronger evidence in favor of the alternative hypothesis, where 2 represents two standard errors above the hypothesized mean of 30. Assuming our test statistic follows a standard normal distribution instead of a t-distribution for simplicity, if we set alpha to 0.05, we would reject the null hypothesis because the test statistic lies above the critical value of 1.645 corresponding to an alpha of 0.05.

Now, imagine if we set alpha to 0.04, resulting in a slightly closer critical value than 1.645. What if we found the exact error rate where the critical value aligns exactly with 2? That would be equivalent to calculating the probability of obtaining a test statistic as large or larger than 2 under the null hypothesis, which is nothing other than the p-value we calculated. In essence, the p-value represents the smallest alpha level for which we would still reject the null hypothesis. Hence, it is referred to as the attained significance level. The advantage of the p-value is that it provides a convenient test statistic that can be interpreted by others. When you report a p-value, the reader or recipient can perform the hypothesis test at any alpha level they choose. The simple rule is that if the p-value is less than the chosen alpha level, the null hypothesis is rejected. If the p-value is greater than the alpha level, the null hypothesis is not rejected. For one-sided hypothesis tests using t-tests or z-tests, the calculated p-value already accounts for evidence as extreme or more extreme in one direction. However, for two-sided hypothesis tests, where evidence in both tails is considered equally probable, the p-value needs to be doubled.

Note: Most statistical software automatically interprets the p-value as a two-sided test for most cases. If it's not explicitly mentioned, the calculated p-value is for the two-sided test. Additionally, in more advanced statistics classes covering tests like the chi-squared test, the calculated p-values are inherently two-sided, and there's no need to double them.

Previously, we discussed an example that illustrates the concept of p-values, and we would like to revisit it now that we have a better understanding. Suppose you have a friend who has had seven girls out of eight kids, and you want to determine the probability that the coin lands on a girl, denoted by p. We are interested in testing whether p is equal to 0.5 or greater than 0.5, with the null hypothesis $H_0: p = 0.5$ and the alternative hypothesis $H_a: p > 0.5$.

Under the null hypothesis, we calculate the probability of obtaining evidence as extreme or more extreme. In this case, the most logical test statistic is the count of girls out of eight. The p-value calculation involves considering the binomial probability of observing seven or eight girls, assuming $p=0.5$. This calculation yields a p-value of approximately 3.5%. Alternatively, you can use the `pbinom` function to directly calculate the p-value, resulting in the same value. If we were conducting a hypothesis test, we would reject the null hypothesis at a 5% level and also at a 4% level. However, we would not reject it at a type 1 error rate of 3%.


```r
choose(8, 7) * .5 ^ 8 + choose(8, 8) * .5 ^ 8 
pbinom(6, size = 8, prob = .5, lower.tail = FALSE)
```

In this specific problem, the calculation of the two-sided p-value is not obvious. To address this, a simple trick is to calculate the two one-sided p-values. For instance, the probability of having seven or more girls represents one-sided p-value, and the probability of having seven or fewer girls represents the other one-sided p-value. Taking the smaller of these two p-values and doubling it gives us the two-sided p-value for binomial exact calculations.

Example(Poisson): Imagine a hospital that has an infection rate of 10 infections per 100 person-days at risk, equivalent to a rate of 0.1 infections per person-day at risk during the last monitoring period. The hospital considers a rate of 0.05 infections per person-day at risk as an important benchmark, and they would implement quality control procedures if the rate exceeds this threshold. However, they don't want to trigger these procedures based on random fluctuations alone. To formally test this hypothesis, accounting for the data's uncertainty, we assume that the count of infections follows a Poisson distribution.

The null hypothesis states that $\lambda$ (the rate) is 0.05, while the alternative hypothesis suggests that $\lambda$ is greater than 0.05. In this case, we want to determine the probability of observing 10 or more infections if the true infection rate for 100 person-days at risk is 5. Using the `ppois` function in R, we calculate the upper tail probability. Due to a quirk in R's syntax, we need to input 9 instead of 10 as the value and set `lower.tail = FALSE` to obtain the probability of strictly greater than 9.


```r
ppois(9, lambda = 5, lower.tail = FALSE)
```

The resulting p-value indicates the probability of obtaining 10 or more infections when the true rate is 5 for 100 person-days at risk. In this example, the probability is approximately 3%, suggesting a relatively low likelihood of observing as many as 10 infections for 100 person-days at risk. Thus, the hospital may consider implementing quality control procedures.

To summarize, the calculation of a p-value involves determining the probability of obtaining data as extreme or more extreme than what was observed in favor of the alternative hypothesis, with the probability calculation performed under the null hypothesis. This approach applies to all p-values, and we have explored the formal rules for executing z-tests and t-tests, as well as examples involving the binomial distribution and the Poisson distribution.

## Knitr

In the following, we give you some information about `knitr` if you are interested you can checkout, Roger Peng's course on Reproducible Research.

Open RStudio and navigate to "File" (Alt-F) and select "New File." You'll see various options, and you should choose "R Markdown." This will generate a simple Knitr document for you. Feel free to edit the title; for example, you can rename it to "Test Knitr Document." In the document, you'll notice R commands and some formatting. To execute R code within the document, you need to use a set of three backticks or quotation marks (usually found below the Escape key on the keyboard). After the initial backticks, add an "r" to indicate that you're using R code, followed by a comma to open up additional options.

Knitr offers numerous options, but we will highlight a few essential ones. The `cache` option determines whether R should store the code's results. Another useful option is `eval`, which specifies whether the code should be evaluated or simply displayed in the document. You can choose to display code with results or hide code using the `results` option. Additionally, the `echo` option controls whether the code is displayed or not. For example, the document includes a code snippet demonstrating a plot using the command `plot(cars)`. Feel free to modify and add your own code snippets.

![(\#fig:unnamed-chunk-24)Knitr rendered Rmd file](resources/images/week_03_files/figure-docx//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_163.png)

Once you've made your changes, save the document. Give it a name like "test.Rmd." To knit the document into an HTML format, you can either use the Knit HTML button in the toolbar or go to `Code` and select "Knit Document." This will create an HTML document displaying the code, results, and any additional content.

If you want to view the HTML document, you can find it in the working directory. In R, you can use the `dir()` function to see the files, and then use the `browseURL()` function to open the HTML document in a web browser. For example, `browseURL("test.html")` will open the HTML document named "test.html."

That's the basic process of using Knitr in a nutshell. Feel free to explore the additional options and customization features to create dynamic and interactive documents.

## Practical R Exercises in swirl

During this week of the course you should complete the following lessons in the Statistical Inference swirl course:

1. T Confidence Intervals
2. Hypothesis Testing
3. P Values
