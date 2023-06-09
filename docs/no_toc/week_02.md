
# Variability, Distribution, & Asymptotics

## Variability
An important characterization of a population is how spread out it is. One of the key measures of spread is variability. We measure population variability with the sample variance, or more often we consider the square root of it, called the standard deviation. The reason for taking the standard deviation is because that measure has the same units as the population. So if our population is a length measurement in meters, the standard deviation is in meters (whereas the variance is in meters squared). Variability has many important uses in statistics. First, the population variance is itself an intrinsically interesting quantity that we want to estimate. Secondly, variability in our estimates is what makes them not imprecise. An important aspect of statistics is quantifying the variability in our estimates.

In the previous lecture, we discussed the population mean as a measure of the center of a distribution. Now, let's explore another important property called variance, which describes the spread or concentration of the density around the mean. If we imagine a bell curve, the probability density function will shift to the left or right as the mean changes. Variance quantifies how widely or narrowly the density is distributed around the mean.
<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_19.png" alt="Alternative text" width="480" />
<p class="caption">(\#fig:unnamed-chunk-1)Variance of a distribution</p>
</div>


```r
library(ggplot2)
x <- seq(-4, 4, length.out = 1000)
mean <- 0

# Standard deviations
sds <- c(0.5, 1, 1.5, 2)

# Create the plot
plot(x, dnorm(x, mean = mean, sd = sds[1]), type = "l", lwd = 3, xlab = "x", ylab = "",yaxt="n")

# Add lines for the other distributions with different colors
colors <- c("blue", "green", "orange", "purple")
for (i in 1:length(sds)) {
  lines(x, dnorm(x, mean = mean, sd = sds[i]), col = colors[i], lwd = 3)
}

# Add legend
legend("topright", legend = paste("sd=", sds), col = colors, lwd = 2,box.lwd = 0, box.col = "white")
```

The blue-colored density represents a standard normal distribution with a standard deviation of 0.5. As the standard deviation increases, the density becomes flatter and spreads more into the tails. Consequently, if a variable is from a normal distribution with a standard deviation of 1.5, they are more likely to have a value beyond 2 compared to a variable from a normal distribution with a standard deviation of 1.

For a random variable X with a mean μ, the variance is precisely the expected squared distance between the random variable and the mean. 
$$ Var(X)=E[(X-\mu)^2]$$
There's also a useful shortcut: 
$$ Var(X)=E[X^2]-E[X]^2$$

Densities with higher variance are more spread out compared to those with lower variances. The square root of variance is known as the standard deviation, which is expressed in the same units as X.

Example: In the previous lecture, we found that the expected value of X, when rolling a die, is 3.5. 
$$ Var(X)=E[X^2]-E[X]^2$$
To calculate the expected value of X squared, we square each number (1, 2, 3, 4, 5) and multiply them by their associated probabilities. Summing these values gives us 15.17.
$Var(X)=15.17 - 3.5^2= 2.92$

Example: Consider tossing a coin with a probability of heads, $p$. From the previous lecture, we know that the expected value of a coin toss is $p$. When calculating the expected value of X squared, 0 squared is 0, and 1 squared is 1. Thus, the expected value of X squared is $p$. Plugging these values into our formula, we get $Var(X)=p - p^2$, which simplifies to $p(1 - p)$. This formula is widely recognized and we suggest you memorize it.

Similar to the relationship between population mean and sample mean, the population variance and sample variance are directly analogous. The population mean represents the center of mass of the population, while the sample mean represents the center of mass of the observed data. Similarly, the population variance quantifies the expected squared distance of a random variable from the population mean, while the sample variance measures the average squared distance of the observed data points from the sample mean. 
$$ S^2=\frac{\Sigma_{i=1}(X_i-\bar X)^2}{n-1}$$
Note that in the denominator of the sample variance formula, we divide by $n- 1$ instead of $n$.

Recall that the sample variance is a function of the data, making it a random variable with its own population distribution. The expected value of this distribution corresponds to the population variance being estimated by the sample variance. As we gather more data, the distribution of the sample variance becomes increasingly concentrated around the population variance it seeks to estimate.

### Variance simulation examples
Suppose we simulate ten standard normal random variables and calculate their sample variance. If we repeat this process many times, we will obtain a distribution of sample variances. This distribution, represented by the salmon-colored density, emerges from repeating the process thousands of times. If we sample enough data points, the center of mass of this distribution will precisely match the variance of the original population we were sampling from—the standard normal distribution with a variance of one.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_69.png" alt="Alternative text" width="480" />
<p class="caption">(\#fig:unnamed-chunk-3)Variance of a distribution of die roll</p>
</div>


```r
library(ggplot2)
nosim <- 10000; 
dat <- data.frame(
    x = c(apply(matrix(rnorm(nosim * 10), nosim), 1, var),
          apply(matrix(rnorm(nosim * 20), nosim), 1, var),
          apply(matrix(rnorm(nosim * 30), nosim), 1, var)),
    n = factor(rep(c("10", "20", "30"), c(nosim, nosim, nosim))) 
    )
ggplot(dat, aes(x = x, fill = n)) + geom_density(size = 2, alpha = .2) + geom_vline(xintercept = 1, size = 2) 
```

The same holds true when we consider sample variances based on 20 observations from the standard normal distribution. we repeat the process of sampling 20 standard normals, calculating the sample variance, and obtaining a distribution of sample variances. This distribution, depicted in a more aqua color, is also centered at one. The pattern continues when we examine sample variances based on 30 observations. However, what's interesting to note is that as the sample size increases, the variance of the population distribution of the sample variances becomes more concentrated. In simpler terms, collecting more data leads to a better and more tightly focused estimate of what the sample variance is trying to estimate. In this case, all the sample variances are estimating a population variance of one because they are sampled from a population with a variance of one.

Before we found that the variance of a die roll was $2.92$. Now, imagine if we were to roll ten dice and calculate the sample variance of the numbers on the sides facing up. By repeating this process numerous times, we can obtain a reliable understanding of the population distribution of the variance of ten die rolls. Although it requires a large number of repetitions, with the help of a computer, we can simulate this process thousands of times, as demonstrated here. 
<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_72.png" alt="Alternative text" width="480" />
<p class="caption">(\#fig:unnamed-chunk-5)Variance of a distribution of coin toss</p>
</div>


```r
dat <- data.frame(
  x = c(apply(matrix(sample(1 : 6, nosim * 10, replace = TRUE), 
                     nosim), 1, var),
        apply(matrix(sample(1 : 6, nosim * 20, replace = TRUE), 
                     nosim), 1, var),
        apply(matrix(sample(1 : 6, nosim * 30, replace = TRUE), 
                     nosim), 1, var)
        ),
  size = factor(rep(c(10, 20, 30), rep(nosim, 3))))
g <- ggplot(dat, aes(x = x, fill = size)) + geom_histogram(alpha = .20, binwidth=.3, colour = "black") 
g <- g + geom_vline(xintercept = 2.92, size = 2)
g + facet_grid(. ~ size)
```
Notice that the distribution of the variance of ten die rolls is precisely centered around 2.92, which is the variance of a single die roll. As I increase the number of dice to 20 and 30, the center of the distribution remains the same, but it becomes more concentrated around the true population variance. This indicates that the sample variance provides a good estimate of the population variance. As we collect more data, the distribution of the sample variance becomes increasingly concentrated around the true value it aims to estimate, demonstrating its unbiasedness.

The reason we divide by $n - 1$ instead of $n$ in the denominator of the sample variance formula is to ensure its unbiasedness.
### Standard error of the mean
Now that we have extensively discussed variances and briefly touched upon the distribution of sample variances, let's revisit the distribution of sample means. It is important to remember that the average of numbers sampled from a population is a random variable with its own population mean and population variance. The population mean remains the same as the original population, while the variance of the sample mean can be related to the variance of the original population. Specifically, the variance of the sample mean decreases to zero as more data is accumulated. 
$$ Var(\bar X)=\frac{\sigma^2}{n}$$
This means that the sample mean becomes more concentrated around the population mean it is trying to estimate, which is a valuable characteristic since we usually only have one sample mean in a given dataset.

Although we do not have multiple repeated sample means to investigate their variability like we do in simulation experiments, we can still estimate the population variance, denoted as $\sigma^2$, using the available data. With knowledge of $\sigma^2$ and the sample size (denoted as n), we can gather valuable information about the distribution of the sample mean. The square root of the statistic, sigma over square root n, is referred to as the standard error of the mean, denoted as the standard deviation of the distribution of a statistic. The term "standard error" is used to represent the variability of means, while the standard error of a regression coefficient describes the variability in regression coefficients.

In summary, considering a population with a mean $\mu$ and variance $\sigma^2$, when we draw a random sample from that population and calculate the variance $S^2$, it serves as an estimate of $\sigma^2$. Similarly, when we calculate the mean, it estimates $\mu$ (population mean). However, $S^2$ (sample variance) is also a random variable with its own distribution centered around $\sigma^2$, becoming more concentrated around it as more observations contribute to the squared value. Additionally, the distribution of sample means from that population is centered at $\mu$ and becomes more concentrated around $\mu$ as more observations are included. Moreover, we precisely know the variance of the distribution of sample means, which is $\sigma^2$ divided by n.

Since we lack repeated sample means in a given dataset, we estimate the sample variance of the mean as $S^2$ divided by n and the logical estimate of the standard error as $\frac{S}{\sqrt{n}}$. The standard error of the mean (or the sample standard error of the mean) is defined as $\frac{S}{\sqrt{n}}$. The standard deviation (S) is an estimate of the variability of the population, while the standard error ($\frac{S}{\sqrt{n}}$) represents the variability of averages of random samples of size n from the population.

Example: If we take standard normals (with a variance of one), the standard deviation of means of n standard normals is expected to be one over $\frac{1}{\sqrt{n}}$. By simulating multiple draws of ten standard normals and calculating their mean, followed by taking the standard deviation of these averages, we should obtain an approximate value of $\frac{1}{\sqrt{n}}$. 

You can explore this using the following code snippet in R:


```
## [1] 0.3116085
```

```
## [1] 0.3162278
```

Similar simulations can be performed for standard uniforms (variance of $\frac{1}{12}$), Poisson(4)  distributions (variance of 4), and coin flips (variance of $p*(1-p)$, assuming p=0.5 then $Var(X)=0.25$). The results of these simulations should align with the theoretical values predicted by our rule.

Understanding the standard error of the mean is crucial in determining the variability of sample means. Simulation experiments can help illustrate these concepts, especially when investigating the distribution of sample means and estimating their standard error.


Example: Consider the father-son data from UsingR library. We will focus on the height of the sons, with "n" representing the number of observations as usual. If we plot a histogram of the son's height and overlay it with a continuous density estimate, we observe a distribution that closely resembles a Gaussian curve. 
<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_75.png" alt="Alternative text" width="480" />
<p class="caption">(\#fig:unnamed-chunk-9)Histogram of son heights</p>
</div>


```r
library(UsingR); data(father.son); 
x <- father.son$sheight
n<-length(x)
g <- ggplot(data = father.son, aes(x = sheight)) 
g <- g + geom_histogram(aes(y = ..density..), fill = "lightblue", binwidth=1, colour = "black")
g <- g + geom_density(size = 2, colour = "black")
g
```

This density estimate provides an approximation of the population density, given the finite amount of data we have collected. The histogram's variability, which the sample variance calculates, serves as an estimate of the variability in son's height from the population this data was drawn from, assuming it was a random sample.

By calculating the variance of x, variance of x divided by n, standard deviation of x, and standard deviation of $\frac{x}{\sqrt{n}}$, and rounding them to two decimal places, we obtain 7.92 and 2.81 as the variance of x and the standard deviation of x, respectively. 

```r
library(UsingR); data(father.son); 
x <- father.son$sheight
n<-length(x)
round(c(var(x), var(x) / n, sd(x), sd(x) / sqrt(n)),2)
```
These numbers represent the variability in son's heights from the dataset and act as estimates of the population variability of son's heights if we assume these sons are a random sample from a meaningful population. In this case, we prefer the value 2.81 over 7.92 since 7.92 is expressed in inches squared, while 2.81 is expressed in inches. Working with the actual units is more intuitive.
Moving on to 0.01 and 0.09, these values no longer reflect the variability in children's heights. Instead, they represent the variability in averages of ten children's heights. The value 0.09 is particularly meaningful as it represents the standard error or the standard deviation in the distribution of averages of n children's heights. While it's an estimate based on the available data, it's the best estimate we can derive from the dataset.

In this section we covered several complex topics, but at its core, understanding variability is the key to understanding statistics. In fact, grasping the concept of variability might be the most crucial aspect of statistics. Here's a summary of our findings: the sample variance provides an estimate of the population variance, and the distribution of the sample variance is centered around the value it is estimating, indicating an unbiased estimation. Moreover, as more data is collected, the distribution becomes more concentrated around the estimated value, leading to a better estimate. We have also gained insights into the distribution of sample means. In addition to knowing its center, as discussed in the previous lecture, we now understand that the variance of the sample mean is the population variance divided by n, and its square root, $\frac{\sigma}{\sqrt{n}}$ is known as the standard error. These quantities capture the variability of averages drawn from the population, and surprisingly, even though we only have access to one sample mean in a given dataset, we can make substantial inferences about the distribution of averages from random samples. This knowledge provides us with a solid foundation for various statistical analyses and methodologies.
## Distributions
Some probability distributions are so important that we need to internalize their characteristics. Here we will cover the most important probability distributions.
### Binomial distribution
Perhaps the simplest distribution is known as the Bernoulli distribution, named after Jacob Bernoulli, a renowned mathematician from a distinguished family of mathematicians. If you're interested, you can explore the Bernoulli family further through their Wikipedia pages. The Bernoulli distribution originates from a coin flip, where a "0" represents tails and a "1" represents heads. We can consider a potentially biased coin with a probability "p" for heads and "1 - p" for tails. The Bernoulli probability mass function is typically denoted as:
$$P(X=x)=p^x * (1 - p)^(1 - x)$$ 
As we have seen before, the mean of a Bernoulli random variable is $p$, and the variance is $p* (1 - p)$. In the context of a Bernoulli random variable, we often refer to "x = 1" as a success, irrespective of the specific definition of success in a given scenario, and "x = 0" as a failure.

A binomial random variable is obtained by summing up a series of independent and identically distributed (iid) Bernoulli random variables. Essentially, a binomial random variable represents the total number of heads obtained in a series of coin flips with a potentially biased coin. Mathematically, if we let $X_1$ to $X_n$ be iid Bernoulli variables with parameter $p$, then the sum of these variables, denoted as $X$, is a binomial random variable.
$$X=\Sigma_{i=1}^n X_i$$
$$P(X=x)=\left(\begin{array}{c}  n \\ x \end{array}\right)p^x(1 - p)^{n-x}=\frac{n!}{x!(n-x)!}p^x(1-p)^{n-x}$$
The binomial probability mass function closely resembles the Bernoulli mass function, but with the inclusion of "n choose x" in front. The notation "n choose x" represents the binomial coefficient, calculated as $\frac{n!}{x!(n-x)!}$. It is worth noting that "n choose 0" $\left(\begin{array}{c}  n \\ 0 \end{array}\right)$ and "n choose n" $\left(\begin{array}{c}  n \\ n \end{array}\right)$ both equal 1. This coefficient helps solve a common combinatorial problem, counting the number of ways to select "x" items out of "n" without replacement while disregarding the ordering of the items.

Example: Suppose your friend has eight children, with seven of them being girls (and no twins). Assuming each gender has an independent 50% probability for each birth, what is the probability of having seven or more girls out of eight births?
We can apply the binomial formula to calculate this probability: 
$$P(X\geq7) = \left(\begin{array}{c}  8 \\ 7 \end{array}\right) * 0.5^7 * (1 - 0.5)^1 + \left(\begin{array}{c}  8 \\ 8 \end{array}\right) * 0.5^8 * (1 - 0.5)^0≈0.04$$

In the provided R code, you can find the implementation of this calculation. Furthermore, for most common distributions, including the binomial distribution, there are built-in functions in R. For example, the `pbinom` function can be used to obtain these probabilities conveniently.

```r
choose(8, 7) * .5 ^ 8 + choose(8, 8) * .5 ^ 8 
pbinom(6, size = 8, prob = .5, lower.tail = FALSE)
```
### Normal distribution
Probabilities play a crucial role in statistics, and among all the distributions, the normal distribution stands out as the most important one. In the upcoming lecture, we will explore why it holds such significance. In fact, if all distributions were to gather and elect a leader, the normal distribution would undoubtedly take the crown.

A random variable that follows a normal (Gaussian) distribution with a mean of $\mu$ and a variance of $\sigma^2$. This distribution is characterized by a density function that resembles a bell curve. If we have a random variable X with this density, its expected value is μ, and its variance is $\sigma^2$. We can express this concisely as $X \sim N(\mu,\sigma^2)$, denoting a normal distribution with mean μ and variance $\sigma^2$. When μ=0 and σ=1, the resulting distribution is known as the *standard normal distribution*. Standard normal random variables are often denoted by the letter $z$. Here, we depict the standard normal density function, which represents the famous bell curve you have likely encountered before.

```r
x <- seq(-3, 3, length = 1000)
library(ggplot2)
g <- ggplot(data.frame(x = x, y = dnorm(x)), 
            aes(x = x, y = y)) + geom_line(size = 2)
g <- g + geom_vline(xintercept = -3 : 3, size = 2)
g
```

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_78.png" alt="Alternative text" width="480" />
<p class="caption">(\#fig:unnamed-chunk-14)Standard normal distribution</p>
</div>

It is important to note that for the standard normal distribution, the mean is 0, and the standard deviation (and variance) is 1. In the diagram, we illustrate one standard deviation above and below the mean, two standard deviations above and below the mean, and three standard deviations above and below the mean. The units on the standard normal distribution can be interpreted as standard deviation units. Additionally, it is worth mentioning that statisticians often find it convenient to revert to the standard normal distribution when discussing normal probabilities, even when dealing with non-standard normal distributions. Therefore, if you want to calculate the probability that a non-standard normal lies between $μ + 1σ$ and $μ - 1σ$ (where μ and σ are specific to its distribution), the probability area is equivalent to that between -1 and +1 on the standard normal distribution. In essence, all normal distributions have the same underlying shape, with the only difference being the units along the axis. By reverting to standard deviations from the mean, all probabilities and calculations can be transformed back to those associated with the standard normal distribution.

Some fundamental reference probabilities related to the standard normal distribution can be easily explained using the graph above as visual aids. First, consider one standard deviation from the mean in the standard normal distribution (or any normal distribution). Approximately 34% of the distribution lies on each side, resulting in a total area of 68% within one standard deviation. Moving on to two standard deviations, denoted by the magenta area in the diagram, around 95% of the distribution falls within this range for any normal distribution. This leaves 2.5% in each tail, and we often utilize this information when calculating confidence intervals. Lastly, when considering three standard deviations from the mean, the area encompasses approximately 99% of the distribution's mass, although it may be difficult to discern from the diagram. *These reference probabilities are essential to commit to memory.*

Probabilities are a fundamental concept, and the normal distribution holds a special place in statistics. Understanding its properties and the relationship to the standard normal distribution allows us to solve problems effectively. All normal distributions share the same essential shape, differing only in their units along the axis. By leveraging the standard normal distribution and converting the non standard normals to standard normals, we can simplify calculations and derive consistent results. The primary difference between different normal distributions lies in the units along the axis. When discussing normal probabilities and converting to standard deviations from the mean, all probabilities and calculations revert back to those associated with the standard normal distribution.

Rules for converting between standard and non-standard normal distributions. If we have a random variable X that follows a normal distribution with a mean of μ and variance of σ squared, we can convert the units of X to standard deviations from the mean by subtracting the mean μ and dividing by the standard deviation σ. If $X \sim N(\mu,\sigma^2)$ then:
$$Z = \frac{X -\mu}{\sigma} \sim N(0, 1)$$
The resulting random variable Z will follow a standard normal distribution. Conversely, if we start with a standard normal random variable Z and want to convert back to the units of the original data, we multiply Z by σ and add μ.
If $Z$ is standard normal $$X = \mu + \sigma Z \sim N(\mu, \sigma^2)$$
The resulting random variable X will then follow a non-standard normal distribution with a mean of μ and variance of $\sigma^2$. 

Standard normal quantiles that are important to remember -1.28 is a quantile such that 10% of the density lies below it, and 90% lies above it. By symmetry, 1.28 on the standard normal distribution represents the quantile at which 10% lies above it. For a potentially non-standard normal distribution, this point would be $μ + 1.28σ$. Another crucial quantile is 1.96 (often approximated as 2), where -1.96 represents the point below which 2.5% of the mass of the normal distribution lies, and +1.96 represents the point above which 2.5% of the mass lies. This implies that 95% of the distribution lies between these two points. For a potentially non-standard normal distribution, these points would be $μ - 1.96σ$ and $μ + 1.96σ$, respectively. It is worth noting that when μ equals 0 and σ equals 1 for the standard normal distribution, the calculation of 1.96 directly yields the correct value.

Example: Determine the $95^{th}$ percentile of a normal distribution with mean μ and variance σ squared. 
In other words, we seek the value $X_{0.95}$ such that 95% of the distribution lies below it. This value represents the threshold if we were to draw samples from this population.

We can find the point $X_{0.95}$, which represents the $95^{th}$ percentile of a normal distribution, by utilizing the q qualifier for the density in R. In this case, we can use the function `qnorm` with the desired quantile 0.95.


```r
qnorm(0.95, mean = mu, sd = sd)
```

It's crucial to input the mean μ and the standard deviation σ (not the variance) into the function. By using `qnorm` with the specified parameters, we can directly obtain the desired value. Another approach to solving this is by leveraging our memorized standard normal quartiles. Since we know that 1.645 standard deviations from the mean corresponds to a quantile with 95% lying below it and 5% lying above it for the standard normal distribution (centered at 0 with standard deviation units from the mean), we can apply this concept to a non-standard normal distribution as well. To calculate the desired point, we can simply compute $μ + σ * 1.64$.

Example: What is the probability that a non-standard normal distribution $N(\mu,\sigma^2)$ is larger than $x$?
To answer this question in R, we can use the `pnorm` function with the specified values of $x$, $mean(\mu)$, and standard deviation $(\sigma)$. It's important to remember to input the sigma value rather than the $\sigma^2$ value to avoid incorrect results. Additionally, we set the argument `lower.tail = FALSE` to indicate that we are interested in the upper tail of the distribution. Alternatively, we can omit this argument and calculate $1 -pnorm(x,mean=\mu,sd=\sigma)$ to achieve the same result.

A conceptually easy way to estimate this probability, which allows us to quickly assess probabilities mentally, is to convert the value x into the number of standard deviations it is from the mean. To achieve this, we compute $(μ -x)/ σ$. The resulting number represents x expressed in terms of how many standard deviations it is from the mean. For example, if the calculated value is approximately two standard deviations from the mean, we can estimate that the probability associated with it is around 2.5%. 

Example: The number of daily ad clicks for companies follows an approximately normal distribution with a mean of 1020 clicks per day and a standard deviation of 50 clicks per day. We want to determine the probability of getting more than 1160 clicks on a given day.

Since $(1160-1020)/50=2.8$ which means 2.8 standard deviation away from the mean, we can infer that this probability will be relatively low. This is because it is nearly 3 standard deviations away from the mean, and we know that such values are located in the tail of the normal distribution. To calculate this probability, we can use the `pnorm` function with the input values of 1,160 for the clicks, a mean of 1,020, and a standard deviation of 50.


```r
pnorm(1160, mean = 1020, sd = 50, lower.tail = FALSE)
pnorm(2.8, lower.tail = FALSE)
```
By setting the argument `lower.tail = FALSE`, we ensure that we obtain the probability of the value being larger than 1,060. The result we obtain is approximately 0.003.

Alternatively, we can directly calculate this probability using the standard normal distribution. By expressing 1,160 as the number of standard deviations it is away from the mean, which is 2.8, we can plug this value into the `pnorm` function with `lower.tail = FALSE` and obtain the same result.


```r
pnorm(2.8, lower.tail = FALSE)
```

Example: Assuming the number of daily ad clicks for the company follows an approximately normal distribution with a mean of 1020 and a standard deviation of 50, we want to find the number of daily ad clicks that represents the point where 75% of the days have fewer clicks.

Since 1020 is both the mean and the median of the specific normal distribution, we know that about 50% of the days lie below this point. Therefore, the desired number of clicks should be greater than 1020. Additionally, one standard deviation above the mean, which corresponds to 1,070. Within this range, we know that 68% of the days lie, leaving 32% outside of it, and 16% in each tail due to the symmetry of the normal distribution. Hence, the desired number of clicks should be around 84% of the distribution, lying between 1,020 and 1,070.

To calculate this quantile, we can use the `qnorm` function with the input value of 0.75, representing the 75th percentile. The mean is set to 1020, and the standard deviation is 50. When we execute this command, `qnorm(0.75, mean = 1020, sd = 50)`, we obtain a number between the previously mentioned range, approximately 1054.

### Poisson distribution

If there were a competition to determine the most useful distribution, the normal distribution would unquestionably win by a wide margin. However, selecting the second most useful distribution would spark a lively debate, with the Poisson distribution being a strong contender. The Poisson distribution is commonly employed to model counts, and its probability mass function is given by 
$$P(X = x; \lambda) = \frac{\lambda^x e^{-\lambda}}{x!}$$
where x represents non-negative integers (0, 1, 2, and so on).
The mean of a Poisson random variable is equal to $\lambda$, and the variance also equals $\lambda$. When modeling with Poisson data, the mean and variance must be equal a condition that can be verified if one has repeated Poisson data.

The Poisson distribution finds utility in various instances. Whenever count data needs to be modeled, especially when the counts are unbounded, the Poisson distribution is a suitable choice. Another prevalent application arises in the field of biostatistics, where event time or survival data is common. For example, in cancer trials, the time until the recurrence of symptoms is modeled using statistical techniques that account for censoring, and these techniques have a strong association with the Poisson distribution. Additionally, when classifying a sample of people based on certain characteristics, creating a contingency table—such as tabulating hair color by race—the Poisson distribution is the default choice for modeling such data. The Poisson distribution is deeply connected to other models, including multinomials and binomials, which might be considered as alternatives.

Another prominent application of the Poisson distribution, though often overlooked due to its commonplace usage, is in cases where a binomial distribution is approximated by the Poisson distribution. This occurs when the sample size (n) is large, and the probability of success (p) is small. Epidemiology, for instance, frequently employs this approximation when dealing with situations where n is large (representing a population) and p is small (indicating the occurrence of rare events). By assuming a Poisson distribution, researchers can effectively model the occurrence rates of events, such as the number of new cases of respiratory diseases in a city as air pollution levels fluctuate. This practice is so prevalent that it is commonly understood within the field without explicit mention.

Example: The number of people showing up at a bus stop follows a Poisson distribution with a mean of 2.5 people per hour. If we observe the bus stop for four hours, we can calculate the probability of three or fewer people showing up during that entire duration. To do this, we apply the Poisson probability formula to the values of three, two, one, and zero, using a rate of 2.5 events per hour multiplied by four hours. The resulting probability is approximately 1%.


```r
ppois(3, lambda = 2.5 * 4)
```

Furthermore, we can discuss the Poisson approximation to the binomial distribution, specifically when the sample size (n) is large, and the probability of success (p) is small. In this scenario, the Poisson distribution can serve as a reasonably accurate approximation for the binomial distribution. To establish notation, let x represent a binomial distribution with parameters n and p, and define $\lambda=n*p$. When n is large and p is small, it is proposed that the probability distribution governing x can be well approximated using Poisson probabilities, where the rate parameter λ is determined as n times p.

Example: In flipping a coin with a success probability of 0.01 for a total of 500 times, we want to calculate the probability of obtaining two or fewer successes. Using the binomial distribution with size 500 and probability 0.01, we obtain approximately 12%. By employing the Poisson approximation with a rate of λ = 500 * 0.01, the result is around 12.5%, which is reasonably close to the binomial calculation.


```r
pbinom(2, size = 500, prob = .01)
ppois(2, lambda=500 * .01)
```

## Asymptotics

Asymptotics are an important topics in statistics. Asymptotics refers to the behavior of estimators as the sample size goes to infinity. Our very notion of probability depends on the idea of asymptotics. For example, many people define probability as the proportion of times an event would occur in infinite repetitions. That is, the probability of a head on a coin is 50% because we believe that if we were to flip it infinitely many times, we would get exactly 50% heads.

We can use asymptotics to help is figure out things about distributions without knowing much about them to begin with. A profound idea along these lines is the *Central Limit Theorem*. It states that the distribution of averages is often normal, even if the distribution that the data is being sampled from is very non-normal. This helps us create robust strategies for creating statistical inferences when we're not willing to assume much about the generating mechanism of our data.

### Asymptotics and LLN

Here we will explore the behavior of statistics as the sample size or some other relevant quantity approaches infinity, which is known as asymptotics. Specifically, we will discuss the case where the sample size tends to infinity.

In the land of asymptopia, everything works out well because there is an infinite amount of data available. Asymptotics play a crucial role in simple statistical inference and approximations. They serve as a versatile tool, akin to a Swiss army knife, allowing us to investigate the statistical properties of various statistics without requiring extensive computations. Asymptotics form the foundation for the frequency interpretation of probabilities. For instance, intuitively, we know that if we flip a coin and calculate the proportion of heads, it should approach 0.5 for a fair coin.

Fortunately, we don't have to delve into the mathematical intricacies of the limits of random variables. Instead, we can rely on a set of powerful tools that enable us to discuss the behavior of sample means from a collection of independently and identically distributed (iid) observations in large samples. One of these tools is the law of large numbers, which states that the average of the observations converges to the population mean it is estimating. For example, if we repeatedly flip a fair coin, the sample proportion of heads will eventually converge to the true probability of a head.

Example: We'll generate a large number of random normal variables and calculate their cumulative means. Initially, there is considerable variability in the means, but as the number of simulations increases, the cumulative means converge towards the true population mean of zero.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_95.png" alt="Cumulative means of random normal variables" width="480" />
<p class="caption">(\#fig:unnamed-chunk-20)Cumulative means of random normal variables</p>
</div>


```r
n <- 10000; means <- cumsum(rnorm(n)) / (1  : n); library(ggplot2)
g <- ggplot(data.frame(x = 1 : n, y = means), aes(x = x, y = y)) 
g <- g + geom_hline(yintercept = 0) + geom_line(size = 2) 
g <- g + labs(x = "Number of obs", y = "Cumulative mean")
g
```

Similarly, we can apply the law of large numbers to the case of coin flipping. By repeatedly flipping a coin and calculating the cumulative means, we observe that the sample proportion of heads converges to the true value of 0.5 as the number of coin flips increases.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_98.png" alt="Cumulative means of coin flips" width="480" />
<p class="caption">(\#fig:unnamed-chunk-22)Cumulative means of coin flips</p>
</div>


```r
means <- cumsum(sample(0 : 1, n , replace = TRUE)) / (1  : n)
g <- ggplot(data.frame(x = 1 : n, y = means), aes(x = x, y = y)) 
g <- g + geom_hline(yintercept = 0.5) + geom_line(size = 2) 
g <- g + labs(x = "Number of obs", y = "Cumulative mean")
g
```

Note: An estimator is considered *consistent* if it converges to the parameter it aims to estimate. For instance, the sample proportion from iid coin flips is consistent for estimating the true success probability of a coin. As we collect more and more coin flip data, the sample proportion of heads approaches the actual probability of obtaining a head. Moreover, not only are sample means consistent estimators, but the sample variance and sample standard deviation of iid random variables are also consistent estimators.

The law of large numbers guarantees the consistency of sample means, but it also applies to sample variances and standard deviations of iid random variables. In other words, these estimators also converge to their respective population counterparts as the sample size increases.

### Asymptotics and the CLT

The Central Limit Theorem (CLT) is perhaps the most important theorem in statistics. It states that the distribution of averages of iid random variables becomes approximately standard normal as the sample size grows. The Central Limit Theorem is remarkably versatile, applying to a wide range of populations. Its loose requirements make it applicable in numerous settings.

To understand the Central Limit Theorem, let's consider an estimate like the sample average $\bar X$. If we subtract its population mean and divide by its standard error the resulting random variable approaches a standard normal distribution as the sample size increases.
$$\frac{\bar{X_n}-\mu}{\sigma/\sqrt{n}}=\frac{\sqrt{n}(\bar{X_n}-\mu)}{\sigma}$$

Importantly, replacing the unknown population standard deviation with the known sample standard deviation does not affect the Central Limit Theorem.

The most useful interpretation of the Central Limit Theorem is that the sample average is approximately normally distributed, with a mean equal to the population mean and a variance given by the standard error of the mean.

Example: Using standard die with the mean of 3.5, and variance of 2.92. We simulate the die roll n times, calculate the sample mean, subtract the population mean, and dividing by the standard error.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_101.png" alt="Distribution of averages of iid random variables in die roll" width="480" />
<p class="caption">(\#fig:unnamed-chunk-24)Distribution of averages of iid random variables in die roll</p>
</div>


```r
nosim <- 1000
cfunc <- function(x, n) sqrt(n) * (mean(x) - 3.5) / 1.71
dat <- data.frame(
  x = c(apply(matrix(sample(1 : 6, nosim * 10, replace = TRUE), 
                     nosim), 1, cfunc, 10),
        apply(matrix(sample(1 : 6, nosim * 20, replace = TRUE), 
                     nosim), 1, cfunc, 20),
        apply(matrix(sample(1 : 6, nosim * 30, replace = TRUE), 
                     nosim), 1, cfunc, 30)
        ),
  size = factor(rep(c(10, 20, 30), rep(nosim, 3))))
g <- ggplot(dat, aes(x = x, fill = size)) + geom_histogram(alpha = .20, binwidth=.3, colour = "black", aes(y = ..density..)) 
g <- g + stat_function(fun = dnorm, size = 2)
g + facet_grid(. ~ size)
```


The distribution approximates a bell curve. As we increase the number of rolls, the approximation improves.

Example: Let $X_i$ be the $0$ or $1$ result of the $i^{th}$ flip of a possibly unfair coin. The sample proportion, say $\hat p$, is the average of the coin flips.
$E[X_i] = p$ and $Var(X_i) = p(1-p)$ Standard error of the mean is $\sqrt{p(1-p)/n}$ Then
$$\frac{\hat p - p}{\sqrt{p(1-p)/n}}$$
will be approximately normally distributed

Flipping a fair coin $n$ times, taking the sample proportion of heads, subtracting off 0.5 and multiply the result by
$2 \sqrt{n}$ divide by $1/(2 \sqrt{n})$ is displayed below.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_104.png" alt="Distribution of averages of iid random variables in coin flip" width="480" />
<p class="caption">(\#fig:unnamed-chunk-26)Distribution of averages of iid random variables in coin flip</p>
</div>


```r
nosim <- 1000
cfunc <- function(x, n) 2 * sqrt(n) * (mean(x) - 0.5) 
dat <- data.frame(
  x = c(apply(matrix(sample(0:1, nosim * 10, replace = TRUE), 
                     nosim), 1, cfunc, 10),
        apply(matrix(sample(0:1, nosim * 20, replace = TRUE), 
                     nosim), 1, cfunc, 20),
        apply(matrix(sample(0:1, nosim * 30, replace = TRUE), 
                     nosim), 1, cfunc, 30)
        ),
  size = factor(rep(c(10, 20, 30), rep(nosim, 3))))
g <- ggplot(dat, aes(x = x, fill = size)) + geom_histogram(binwidth=.3, colour = "black", aes(y = ..density..)) 
g <- g + stat_function(fun = dnorm, size = 2)
g + facet_grid(. ~ size)
```

Taking the result of each flip (0 or 1) as an iid random variable, we calculate the sample proportion of heads $\hat p$. We again obtain a distribution that approximates a bell curve. Similar to the previous example, the approximation improves as the number of coin flips increases.

It's important to note that the speed at which the normalized coin flips converge to normality depends on the bias of the coin. If the coin is heavily biased, the approximation may not be perfect even with a large sample size. However, as the number of coin flips approaches infinity, the Central Limit Theorem guarantees an excellent approximation.

As a fun example, let's discuss Galton's quincunx. This machine, often found in science museums, visually demonstrates the Central Limit Theorem using a game resembling Pachinko. [An image from Wikipedia showing Galton's quincunx](https://upload.wikimedia.org/wikipedia/commons/c/c1/Galton_box.jpg). In Galton's quincunx, a ball falls through a series of pegs, bouncing left or right at each peg. Each bounce can be thought of as a coin flip or binomial experiment. The total number of successes (heads) follows an approximately normal distribution, as predicted by the Central Limit Theorem. At the museum, the balls collect in bins, forming a histogram that aligns with the expected normal distribution.

In summary, the Central Limit Theorem is a powerful tool that allows us to approximate the distribution of averages of iid random variables. It applies to various settings and provides valuable insights into statistical inference. The examples we explored, from dice rolls to coin flips to Galton's quincunx, illustrate the practical applications of the Central Limit Theorem and the convergence to a standard normal distribution as the sample size increases.

### Asymptotics and confidence intervals

The central limit theorem tells us that the sample mean follows an approximately normal distribution with a population mean of μ and a standard deviation of $\sigma/ \sqrt{n}$. This distribution allows us to make inferences about the population mean based on sample data. When considering the distribution, we observe that $μ+2$ standard errors is quite far out in the tail, with only a 2.5% chance of a normal value being larger than two standard deviations in the tail.
Similarly, $μ-2$ standard errors is far in the left tail, with only a 2.5% chance of a normal value being smaller than two standard deviations in the left tail. Therefore, the probability that the sample mean $\bar X$ is greater than $μ+2$ standard errors or smaller than $μ-2$ standard errors is 5%. Equivalently, the probability that μ is between these limits is 95%. By reversing the roles of $\bar X$ and μ, we can conclude that the interval $[\bar X - 2 \sigma /\sqrt{n}, \bar X + 2 \sigma /\sqrt{n}]$ contains μ with a probability of 95%.

It's important to note that in this interpretation, we treat the interval $[\bar X - 2 \sigma /\sqrt{n}, \bar X + 2 \sigma /\sqrt{n}]$ as random, while μ is fixed. This allows us to discuss the probability that the interval contains μ. In practice, if we repeatedly obtain samples of size n from the population and construct a confidence interval in each case, about 95% of the intervals will contain μ, the parameter we are trying to estimate. If we want a 90% confidence interval, we need 5% in each tail, so we would use a different multiplier instead of 2 (e.g., 1.645).

Example: Using the father-son data from the "Using R" package we want to estimate the average height of sons $\bar X$. We can calculate the mean of the sample plus or minus the 0.975th normal quantile times the standard error of the mean.


```r
library(UsingR)
data(father.son)
x <- father.son$sheight
(mean(x) + c(-1, 1) * qnorm(.975) * sd(x) / sqrt(length(x))) / 12
```

Dividing by 12 ensures that our confidence interval is in feet rather than inches. If we obtain a confidence interval of 5.710 to 5.738, we can say that if the sons' height in this data are a random sample from the population of interest, the confidence interval for the average height of the sons would be 5.71 to 5.74.

Another application is when dealing with coin flips and estimating the success probability $p$ of the coin. Each observation $X_i$ in this case is either 0 or 1, with a common success probability $p$. The variance of a coin flip is $p * (1 - p)$, where p is the true success probability of the coin. The standard error of the mean is then:
$$ \hat p \pm z_{1 - \alpha/2}  \sqrt{\frac{p(1 - p)}{n}}$$

Since we don't know the true value of $p$, we replace it with the estimated value $\hat p$. This type of confidence interval is known as the *Wald confidence interval*, named after the statistician Wald. When p equals 0.5, the variance $p(1 - p)$ is maximized, resulting in a standard error of 0.5. Multiplying it by 2 in the 95% interval cancels out, leaving the following expression for a 95% confidence interval, which is a quick estimate for p:
$$\hat p \pm 1/\sqrt{n}$$

Example: Imaging you are running for political office, and in a random sample of 100 likely voters, 56 intend to vote for you. To determine if you can relax or if you need to campaign more, you can use a quick calculation. 

With the information from the sample you can with with probability of 0.56 taking $\frac{1}{\sqrt{100}}=0.1$ means the approximate 95% interval is 0.46 to 0.66. The confidence interval suggests that we cannot rule out possibilities below 0.5 with 95% confidence. Therefore, you shouldn't relax and should continue campaigning.

As a general guideline, you typically need at least 100 observations for one decimal place in a binomial experiment, 10,000 for two decimal places, and a million for three decimal places. These numbers reflect the approximate sample sizes needed for accurate estimation.

In summary, the central limit theorem provides us with a practical tool for constructing confidence intervals and making inferences about population parameters. It allows us to estimate the population mean using the sample mean and provides a measure of uncertainty through confidence intervals. The Wald confidence interval is a useful approximation for estimating the success probability in binomial experiments. Additionally, considering the sample size helps determine the level of precision and confidence in our estimates.

Consider a simulation where we repeatedly flip a coin with a known success probability. The goal is to calculate the percentage of times that the confidence interval covers the true probability. In each simulation, we flip the coin 20 times and vary the true success probability between 0.1 and 0.9 in steps of 0.05. We conduct 1,000 simulations for each true success probability.


```r
n <- 20; pvals <- seq(.1, .9, by = .05); nosim <- 1000
coverage <- sapply(pvals, function(p){
  phats <- rbinom(nosim, prob = p, size = n) / n
  ll <- phats - qnorm(.975) * sqrt(phats * (1 - phats) / n)
  ul <- phats + qnorm(.975) * sqrt(phats * (1 - phats) / n)
  mean(ll < p & ul > p)
})
```

For each true success probability, we generate 1,000 sets of 20 coin flips and calculate the sample proportion. Then, we compute the lower and upper limits of the confidence interval for each set of coin flips. Finally, we determine the proportion of times that the confidence interval covers the true value of the success probability. we store these proportions in a variable called "coverage."

To visualize the results, we can plot the coverage as a function of the true success probability used in the simulation. For example, if the true value of p is 0.5, we perform 1,000 simulations and calculate the coverage based on whether the confidence interval covers 0.5 or not. In this case, the coverage is over 95%, indicating that the confidence interval provides better than 95% coverage for a true success probability of 0.5.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_107.png" alt="Coverage of confidence intervals for coin flips, n=20" width="480" />
<p class="caption">(\#fig:unnamed-chunk-30)Coverage of confidence intervals for coin flips, n=20</p>
</div>

Although there is some Monte Carlo error due to the finite number of simulations, 1,000 simulations generally yield good accuracy. For a true success probability around 12%, the coverage falls well below the expected 95%. The reason behind this discrepancy is that the central limit theorem is not accurate enough for this specific value of n (the number of coin flips) and the true probability. To address this issue for smaller values of n, a quick fix is to add 2 to the number of successes and 2 to the number of failures. This adjustment modifies the sample proportion, making it $\frac{X+2}{n+4}$. After applying this adjustment, the confidence interval procedure can be performed as usual. This modified interval is known as the *Agresti/Coull interval* and tends to perform better than the standard Wald interval. Before demonstrating the results for the adjusted intervals, it is important to note that larger values of n yield better performance. In a simulation where n is increased to 100, the coverage probability improves and remains close to the expected 95% across different values of p.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_110.png" alt="Coverage of confidence intervals for coin flips, n=100" width="480" />
<p class="caption">(\#fig:unnamed-chunk-31)Coverage of confidence intervals for coin flips, n=100</p>
</div>

Returning to the simulation with n=20, when using the add 2 successes and 2 failures interval, the coverage probability is higher than 95%, indicating an improvement compared to the poor coverage of the Wald interval for certain true probability values. However, it's important to balance coverage and interval width, as being too conservative can lead to overly wide intervals. Based on these observations, we strongly recommend using the add 2 successes and 2 failures interval instead of the Wald interval in this specific scenario.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_81.png" alt="Coverage of confidence intervals for coin flips, n=20, add 2 successes and 2 failures" width="480" />
<p class="caption">(\#fig:unnamed-chunk-32)Coverage of confidence intervals for coin flips, n=20, add 2 successes and 2 failures</p>
</div>

Example: Create a Poisson interval using the formula that involves the estimate plus or minus the normal quantile standard error. 
Although the application of the central limit theorem in this case may be less clear, we will discuss it shortly.Consider a nuclear pump that failed 5 times out of 94.32 days over a monitoring period. We want to calculate a 95% confidence interval for the failure rate per day. Assuming the number of failures follows a Poisson distribution with a failure rate of lambda and the monitoring period is denoted as t, the estimate of the failure rate is the number of failures divided by the total monitoring time. The variance of this estimate is $\lambda/t$.


```r
x <- 5; t <- 94.32; lambda <- x / t
round(lambda + c(-1, 1) * qnorm(.975) * sqrt(lambda / t), 3)
poisson.test(x, T = 94.32)$conf
```

In the calculations performed in R, the number of events $x$ is set to 5, and the monitoring time $t$ is 94.32. The rate estimate $\hat \lambda$ is computed as $x/t$, and the confidence interval estimate is obtained by adding or subtracting the relevant standard normal quantile multiplied by the standard error. The resulting interval is rounded to three decimal places. In addition to the large sample interval, we can also calculate an exact Poisson interval using the `poisson.test` function in R. This exact interval guarantees the specified coverage (e.g., 95%), but it may be conservative and result in wider intervals than necessary.

To examine how confidence intervals perform in repeated samplings, let's conduct a simulation similar to the one for the coin example, but for the Poisson coverage rate. We select a range of $\lambda$ values around those from our previous example and perform 1,000 simulations. The monitoring time is set to 100 for simplicity. We define coverage as the percentage of times the simulated interval contains the true $\lambda$ value used in the simulation. The simulation is repeated for various $\lambda$ values, and the resulting plot shows the $\lambda$ values on the x-axis and the estimated coverage on the y-axis.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_84.png" alt="Coverage of confidence intervals for Poisson data" width="480" />
<p class="caption">(\#fig:unnamed-chunk-34)Coverage of confidence intervals for Poisson data</p>
</div>

The plot reveals that as $\lambda$ values increase, the coverage approaches 95%. However, there is some Monte Carlo error due to the finite number of simulations. On the other hand, as the true $\lambda$ value becomes smaller, the coverage deteriorates significantly. For very small $\lambda$ values, the purported 95% interval may only provide 50% actual coverage. To address this issue, it is recommended not to rely on the asymptotic interval for small $\lambda$ values, especially when there are relatively few events during a large monitoring time. In such cases, the asymptotic interval does not align well with the Poisson distribution. Instead, an exact Poisson interval can be used as an alternative.

Although the central limit theorem's application in the Poisson case may not be immediately clear, a simulation with a larger monitoring time (e.g., changing t from 100 to 1,000) demonstrates that as the monitoring time increases, the coverage improves and converges to 95% for most $\lambda$ values. However, some poor coverage may still occur for small $\lambda$ values, which we know the interval has trouble handling. In such cases, the exact Poisson interval remains a viable option.

<div class="figure" style="text-align: center">
<img src="resources/images/week_02_files/figure-html//1U1PiqeXG4XoKmg8hRFJqE1OFDOJfificBz1jLeDunHo_g257d7b8e795_0_122.png" alt="Coverage of confidence intervals for Poisson data, t=1000" width="480" />
<p class="caption">(\#fig:unnamed-chunk-35)Coverage of confidence intervals for Poisson data, t=1000</p>
</div>

To summarize briefly, we covered the Law of Large Numbers, which states that averages of independent and identically distributed (iid) random variables converge to the quantities they are estimating. This applies to Poisson rates as well, although the convergence process may be less clear. As the monitoring time tends to infinity, for example, Poisson rates converge to their estimated values. We also discussed the Central Limit Theorem, which states that averages are approximately normally distributed. These distributions are centered at the population mean, a concept we already knew without the theorem, with standard deviations equal to the standard error of the mean. However, the Central Limit Theorem does not guarantee that the sample size is large enough for this approximation to be accurate. We have observed instances where confidence intervals are very accurate and others where they are less accurate. Speaking of confidence intervals, our default approach for constructing them is to take the mean estimate and add or subtract the relevant normal quantile times the standard error. This method, known as "walled intervals," is used not only in this context but also in regression analysis, general linear models, and other complex subjects. For a 95% confidence interval, the quantile value can be taken as 2 or, for more accuracy, 1.96. Confidence intervals become wider as the desired coverage increases within a specific technique. This is because wider intervals provide more certainty that the parameter lies within them. To illustrate, imagine an extreme scenario where your life depends on the confidence interval containing the true parameter. In this case, you would want to make the interval as wide as possible to ensure your safety. The mathematics behind confidence intervals follows the same principle.
In the cases of Poisson and binomial distributions, which are discrete, the Central Limit Theorem may not accurately approximate their distributions. However, exact procedures exist for these cases. We also learned a simple fix for constructing confidence intervals in the binomial case by adding two successes and two failures, which provides a better interval without requiring complex computations. This method can be easily done by hand or mentally, even without access to a computer.


- The LLN states that averages of iid samples converge to the population means that they are estimating
- The CLT states that averages are approximately normal, with distributions
  - centered at the population mean
  - with standard deviation equal to the standard error of the mean
  - CLT gives no guarantee that $n$ is large enough
- Taking the mean and adding and subtracting the relevant normal quantile times the SE yields a confidence interval for the mean
  - Adding and subtracting 2 SEs works for 95% intervals
- Confidence intervals get wider as the coverage increases (why?)
- Confidence intervals get narrower with less variability or larger sample sizes
- The Poisson and binomial case have exact intervals that don't require the CLT
  - But a quick fix for small sample size binomial calculations is to add 2 successes and failures

## Practical R Exercises in swirl

During this week of the course you should complete the following lessons in the Statistical Inference swirl course:

- Variance
- CommonDistros
- Asymptotics

