
# Population Statistics

## Populations

Almost every statistics text begins with the concept of a
**population**. A population is the complete set of individuals to which
you want to predict values. Let's dwell on this concept, as it is
something that did not hit home for me right away in my career. Again,
the population is all of the individuals for which you are interested in
making a prediction. What do we mean by individuals? Not just people --
individuals can plants, insects, disease, livestock or, indeed, farmers.

Just as important as what individuals are in a population is its extent.
What do you want the individuals to represent? If you are a farmer, do
you want to apply the data from these individuals directly to
themselves, or will you use them to make management decisions for the
entire field, or all the fields in your farm? Will you use the results
this season or to make predictions for future seasons? If you are an
animal nutritionist, will you use rations data to support dairy
Herefords, or beef Angus?

If you are a sales agronomist, will you use the data to support sales on
one farm, a group of farms in one area, or across your entire sales
territory? If you are in Extension, will the individuals you measure be
applicable to your entire county, group of counties, or state? If you
are in industry like me, will your results be applicable to several
states?

This is a very, very critical question, as you design experiments -- or
as you read research conducted by others. To what do or will the results
apply? Obviously, an Iowa farmer should not follow the optimum planting
date determined for a grower in North Carolina, nor should an Ohio
farmer assume our pale clays will be as forgiving as the dark, mellow
loam of southern Minnesota.

Drilling down, you might further consider whether research was conducted
in areas that have similar temperature or rainfall, how different the
soil texture might be to the areas to which you want to apply results.
At the farm level, you might ask how similar the crop rotation, tillage,
or planting methods were to that operation. At the field level, you
might wonder about planting date or the hybrid that was sown.

When you use the results from set of individuals to make predictions
about other individuals, you are making inferences -- you are using
those data to make predictions, whether it be for that same field next
month or next year, or for other locations (areas in a field, fields in
a farm, counties, or states). When we speak of an inference space, then,
that is the total group of individuals to which you will apply your
results. Or, in another word, your population.

In summary, one of the most critical skills you can apply with data
science has no formula and, indeed, little to do with math (at least in
our application). It is to ask yourself, will my experiment represent
the entire population in which I am interested? Indeed, one field trial
likely will not address the entire population in which you are
interested -- it is up to you to determine the population to which you
are comfortable applying those results.

In fact, statistics or data science done without this "domain" knowledge
whether a dataset is useful or experimental results are reasonable can
be disastrous. Just because a model fits one dataset very well or
treatments are significantly different does not mean they should be used
to make decisions. Your competency as an agronomic data scientist
depends on everything you learn in your program of study. Soil science,
crop physiology, and integrated pest management, to name just a few
subjects, are as much a prerequisite as any math course you have taken.

In some cases all of the individuals in a population can be measured --
in such case, we will use the basic statistics described in this unit.
The yield map we will analyze in this unit is a loose example of a case
where we can measure

In most cases, however, it is not physically or financially feasible to
measure all individuals in a population. In that case, subsets of the
population, called samples, are used to estimate the range of
individuals in a population.

## Case Study: Yield Map

For our first case study, we will use a situation where every individual
in our population can be measured: a single soybean field in central
Iowa. In this case, yield data were gathered using a combine monitor. In
case you don't live and breathe field crops like me, combines (the
machines that harvest grain) are usually equipped with a scale that
repeatedly weighs grain as the combine moves across the field. The
moisture of the grain is also recorded. These data are combined with
measures of the combine speed and knowledge of the number of rows
harvested at once to calculate the yield per area of grain, adjusted to
the market standard for grain moisture.

Almost all of you have seen a yield map somewhat like the one below. In
this map, blue circles represent lower yields, while yellow and orange
circles represent higher yields.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-1-1.pdf)<!-- --> 

We will learn in the Exercises portion of this lesson how to create a
map like this using just a few lines of code.

Each dataset has a structure -- the way data are organized in columns
and rows. To get a sense of the structure of our soybean dataset, we can
examine the first 6 rows of the dataset using R.


```
## 
## Attaching package: 'kableExtra'
```

```
## The following object is masked from 'package:dplyr':
## 
##     group_rows
```


\begin{tabular}[t]{r|r|r|r|r|r|l|r|l|r|l|r|l}
\hline
DISTANCE & SWATHWIDTH & VRYIELDVOL & Crop & WetMass & Moisture & Time & Heading & VARIETY & Elevation & IsoTime & yield\_bu & geometry\\
\hline
0.9202733 & 5 & 57.38461 & 174 & 3443.652 & 0.00 & 9/19/2016 4:45:46 PM & 300.1584 & 23A42 & 786.8470 & 2016-09-19T16:45:46.001Z & 65.97034 & POINT (-93.15026 41.66641)\\
\hline
2.6919269 & 5 & 55.88097 & 174 & 3353.411 & 0.00 & 9/19/2016 4:45:48 PM & 303.6084 & 23A42 & 786.6140 & 2016-09-19T16:45:48.004Z & 64.24158 & POINT (-93.15028 41.66641)\\
\hline
2.6263101 & 5 & 80.83788 & 174 & 4851.075 & 0.00 & 9/19/2016 4:45:49 PM & 304.3084 & 23A42 & 786.1416 & 2016-09-19T16:45:49.007Z & 92.93246 & POINT (-93.15028 41.66642)\\
\hline
2.7575437 & 5 & 71.76773 & 174 & 4306.777 & 6.22 & 9/19/2016 4:45:51 PM & 306.2084 & 23A42 & 785.7381 & 2016-09-19T16:45:51.002Z & 77.37348 & POINT (-93.1503 41.66642)\\
\hline
2.3966513 & 5 & 91.03274 & 174 & 5462.851 & 12.22 & 9/19/2016 4:45:54 PM & 309.2284 & 23A42 & 785.5937 & 2016-09-19T16:45:54.002Z & 91.86380 & POINT (-93.15032 41.66644)\\
\hline
3.1840529 & 5 & 65.59037 & 174 & 3951.056 & 13.33 & 9/19/2016 4:45:55 PM & 309.7584 & 23A42 & 785.7512 & 2016-09-19T16:45:55.005Z & 65.60115 & POINT (-93.15033 41.66644)\\
\hline
\end{tabular}

Each row in the above dataset is a **case**, sometimes called an
**instance**. It is an single observation taken within a soybean field.
That case may contain one or more **variables**: specific measurements
or observations recorded for each case. In the dataset above, variables
include *DISTANCE*, *SWATHWIDTH*, *VRYIELDVOL*, *Crop*, *WetMass*, and
many others.

The two most important to us in this lesson are *yield_bu* and
*geometry*. That this dataset has a column named geometry indicates it
is a special kind of dataset called a **shape file** -- a dataset in
which the measures are geo-referenced. That is, we know where on Earth
these measurements were taken. The geometry column in this case
identifies a point with each observation.

## Distributions

At this point, we have two options any time we want to know about
soybean yield in this field. We can pull out this map or the complete
dataset (which has over 6,500 observations) and look at try to
intuitively understand the data. Or we can use statistics which, in a
sense, provide us a formula for approximating the values in our dataset
with just a few numbers.

A **distribution** describes the range of values that occur within a
given variable. What is the range of values in our measured values? In
this example, what are the highest and lowest yields we observed? What
ranges of values occur more frequently? Many times, we want to see
whether the distribution of one

### Histograms

Before we get into the math required to generate these statistics,
however, we should look at the shape of our data. What is the range of
values in our measured values? In this example, what are the highest and
lowest yields we observed? What ranges of values occur more frequently?
Do the observed values make general sense?

One of the easiest and most informative things for us to do is to create
a particular bar chart known as a **histogram**.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-3-1.pdf)<!-- --> 

In the histogram above, each bar represents range of values. This range
is often referred to as a *bin*. The lowest bin includes values from 50
to 59.0000. The next bin includes values from 60 to 69.9999. And so on.
The height of each bar represents the **frequency** within each range:
the number of individuals in that population that have values within
that range.

Each bin can also be defined by its **midpoint**. The midpoint is the
middle value in each range. For the bin that includes values from 50 to
50.9999, the midpoint is 55. For the bin that includes values from 60 to
60.9999, the midpoint is 65.

In the plot above, the midpoint for each bar is indicated by the orange
bar beneath it.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-4-1.pdf)<!-- --> 

There are many ways in which we can draw a histogram -- and other
visualizations -- in R. We will learn more in this course about an R
package called `ggplot2`, which can create just about any plot you might
imagine. Here is a simple taste:


```r
ggplot(data=yield, aes(x=yield_bu)) +
  geom_histogram(breaks=seq(50, 110, 10), fill="tomato", color="black")
```

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-5-1.pdf)<!-- --> 

Varying the *bin width* provides us with different perspectives on our
distribution. Wide bins, which each include a greater range of values,
will provide more gross representations of the data, while narrower bins
will provide greater detail. When bins are too narrow, however, there
may be gaps in our histogram where no values occur within particular
bins.

Throughout this course, I have created interactive exercises to help you
better visualize statistical concepts. Often, they will allow you to
observe how changing the variables or the number of observations can
affect a statistical test or visualization.

These exercises are located outside of this textbook. To access them,
please follow links like that below. The exercises may take several
seconds to launch and run in your browser. I apologize for their
slowness -- this is the best platform I have found to date.

Please click on the link below to open an application where you can vary
the bin width and see how it changes your perspective:

<https://marin-harbur.shinyapps.io/01-app-histogram/>

### Percentiles

We can also use **percentiles** to describe the values of a variable
more numerically. Percentiles describe how values the proportional
spread of values, from lowest to highest, within a distribution. To
identify percentiles, the data are numerically ordered (ranked) from
lowest to highest. Each percentile is associated with a number; the
percentile is the percentage of all data equal to or less than that
number. We can quickly generate the 0th, 25th, 50th, and 75th, and 100th
percentile in R:


```r
summary(yield$yield_bu)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   55.12   74.96   80.62   80.09   85.44  104.95
```

This returns six numbers. The 0th percentile (alternatively referred to
as the minimum) is 55.12 -- this is the lowest yield measured in the
field. The 25th percentile (also called the 1st Quartile) is 74.96. This
means that 25% of all observations were equal to 74.96 bushels or less.
The 50th percentile (also known as the median) was 80.62, meaning half
of all observations were equal to 80.62 bushels or less. 75% of
observations were less than 85.44, the 75th percentile (or 3rd
quartile). Finally, the 100th percentile (or maximum yield) recorded for
this field was 104.95.

We are now gaining a better sense of the range of observations that were
most common. But we can describe this distribution with even fewer
numbers.

### Normal Distribution Model

Let's overlay a curve, representing the **normal distribution**, on our
histogram. You have probably seen or heard of this curve before. Often
it is called a *bell curve*; in school, it is the *Curve* that many
students count on to bring up their grades. We will learn more about
this distribution in *Lesson 2*.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-7-1.pdf)<!-- --> 

In a perfect scenario, our curve would pass through the midpoint of each
bar. This rarely happens with real-world data, and especially in
agriculture. The data may be slightly **skewed**, meaning there are more
individuals that measure above the mean than below, or vice versa.

In this example, our data do not appear skewed. Our curve seems a little
too short and wide to exactly fit the data. This is a condition called
**kurtosis**. No, kurtosis doesn't mean that our data stink; they are
just more spread out or compressed than in a "perfect" situation.

No problem. We can -- and should -- conclude it is appropriate to fit
these data with a normal distribution. If we had even more data, the
curve would likely fit them even better.

Many populations can be handily summarized with the normal distribution
curve, but we need to know a couple of statistics about the data. First,
we need to know where the center of the curve should be. Second, we need
to know the width or dispersion of the curve.

### Measures of Center

To mathamatically describe our distribution, we first need a **measure
of center**. The two most common measures of center are the arithmetic
mean and median. The **mean** is the sum of all observations divided by
the number of observations.

$$\displaystyle \mu = \frac{\sum x_i}{n}$$ The $\mu$ symbol (a u with a
tail) signifies the true mean of a population. The $\sum$ symbol (the
character next to $x_i$ which looks like the angry insect alien from *A
Quiet Place*) means "sum". Thus, anytime you see the $\sum$ symbol, we
are summing the variable(s) to its right. $x_i$ is the value $x$ of the
ith individual in the population. Finally, n is the number of
individuals in the population.

For example, if we have a set of number from 1:5, their mean can be
calculated as:

$$
\frac{1+2+3+4+5}{5} = 3
$$ 

The mean yield for our field is about 80.09 bushels per acre. This
isrepresented by the red line in the histogram below.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-8-1.pdf)<!-- --> 

Earlier, you were introduced to the median. As discussed, the **median**
is a number such that half of the individuals in the population are
greater and half are less. If we have an odd number of individuals, the
median is the "middle" number as the individuals are ranked from
greatest to least.

$$ \{1,2,3,4,5\} \\ median = 3 $$

If we have an even number of measures, the median is the average of the
middle two individuals in the ranking:

$$\{1,2,3,4,5,6\} \\ median = 3.5 $$

Let's add a blue line to our histogram to represent the median.

![](01-Introduction-and-Population-Statistics_files/figure-latex/unnamed-chunk-9-1.pdf)<!-- --> 

As you can see, they are practically identical. When the mean and median
are similar, the number of individuals measuring greater and less than
the mean are roughly equivalent. In this case, our data can be
represented using the normal distribution.

We also need a statistic that tells us how wide to draw the curve. That
statistic is called a measure of dispersion, and we will learn about it
next.

### Measures of Dispersion

To describe the spread of a population, we use one of three related
**measures of disperson**: sum of squares, variance, and standard
deviation. Although there is a little math involved in these three
statistics, please make yourself comfortable with their concepts because
they are *very* important in this course. Almost every statistical test
we will learn during this course is rooted in these measures of
population width.

#### Sum of Squares

The first measure of population width is the **sum of squares**. This is
the sum of the squared differences between each observation and the
mean. The sum of squares of a measurement x is:

$$\displaystyle S_{xx} = (x_i - \mu)^2$$

Where again $x_i$ is the value $x$ of the $ith$ individual in the
population and $\mu$ is the true mean of a population.

Why do we square the differences between the observations and means?
Simply, if we were to add the unsquared differences they would add to
exactly zero. Let's prove this to ourselves. Let's again use the set of
numbers (1, 2, 3, 4, 5). We can measure the distance of each individual
from the mean by subtracting the mean from it. This difference is called
the residual.


```r
sample_data = data.frame(individuals = c(1,2,3,4,5))

library(janitor)
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

```r
first_resid_table = sample_data %>%
  mutate(mean = 3) %>%
  mutate(residual = individuals - mean) %>%
  mutate(mean = as.character(mean)) 

first_resid_totals = data.frame(individuals = "Total",
                                mean = "",
                                residual = sum(first_resid_table$residual))

first_resid_table %>%
  rbind(first_resid_totals) %>%
  kbl()
```


\begin{tabular}[t]{l|l|r}
\hline
individuals & mean & residual\\
\hline
1 & 3 & -2\\
\hline
2 & 3 & -1\\
\hline
3 & 3 & 0\\
\hline
4 & 3 & 1\\
\hline
5 & 3 & 2\\
\hline
Total &  & 0\\
\hline
\end{tabular}

The first column of the above dataset contains the individual
observations. The second column contains the population mean, repeated
for each observation. The third column is the residuals, which are
calculated by subtracting each observed value from the population mean.

And if we sum these residuals we get zero.

$$ (-2) + (-1) + (0) + (+1) + (+2) = 0 $$

Let's now do this with our field data. The number of residuals (almost
6800) is too many to visualize at once, so we will pick 20 at random.


```r
set.seed(080921)
yield_sample = data.frame(yield = sample(yield$yield_bu, 10))

second_resid_table = yield_sample %>%
  mutate(yield = round(yield,2),
         mean = round(mean(yield),2),
         residual = yield-mean) 

second_resid_totals = data.frame(yield = "Total",
                                mean = "",
                                residual = sum(second_resid_table$residual))

second_resid_table %>%
  rbind(second_resid_totals) %>%
  kbl()
```


\begin{tabular}[t]{l|l|r}
\hline
yield & mean & residual\\
\hline
83.61 & 76.52 & 7.09\\
\hline
86.82 & 76.52 & 10.30\\
\hline
68.39 & 76.52 & -8.13\\
\hline
81.91 & 76.52 & 5.39\\
\hline
80.75 & 76.52 & 4.23\\
\hline
57.06 & 76.52 & -19.46\\
\hline
62.58 & 76.52 & -13.94\\
\hline
86.6 & 76.52 & 10.08\\
\hline
80.05 & 76.52 & 3.53\\
\hline
77.42 & 76.52 & 0.90\\
\hline
Total &  & -0.01\\
\hline
\end{tabular}

If we sum up all the yield residuals, we get -0.04. Not exactly zero,
but close. The difference from zero is the result of rounding errors
during the calculation.

The sum of squares is calculated by squaring each residual and then
summing the residuals. For our example using the set (1, 2, 3, 4, 5):


```r
first_squares_table = first_resid_table %>%
  mutate(square = residual^2)

first_squares_totals = data.frame(individuals = "Total",
                                mean = "",
                                residual = "",
                                square = sum(first_squares_table$square))
first_squares_table %>%
  rbind(first_squares_totals) %>%
  kbl()
```


\begin{tabular}[t]{l|l|l|r}
\hline
individuals & mean & residual & square\\
\hline
1 & 3 & -2 & 4\\
\hline
2 & 3 & -1 & 1\\
\hline
3 & 3 & 0 & 0\\
\hline
4 & 3 & 1 & 1\\
\hline
5 & 3 & 2 & 4\\
\hline
Total &  &  & 10\\
\hline
\end{tabular}

And for our yield data:


```r
second_squares_table = second_resid_table %>%
  mutate(square = round(residual^2,2)) %>%
  mutate(residual = round(residual, 2))

second_squares_totals = data.frame(yield = "Total",
                                mean = "",
                                residual = "",
                                square = sum(second_squares_table$square))
second_squares_table %>%
  rbind(second_squares_totals) %>%
  kbl()
```


\begin{tabular}[t]{l|l|l|r}
\hline
yield & mean & residual & square\\
\hline
83.61 & 76.52 & 7.09 & 50.27\\
\hline
86.82 & 76.52 & 10.3 & 106.09\\
\hline
68.39 & 76.52 & -8.13 & 66.10\\
\hline
81.91 & 76.52 & 5.39 & 29.05\\
\hline
80.75 & 76.52 & 4.23 & 17.89\\
\hline
57.06 & 76.52 & -19.46 & 378.69\\
\hline
62.58 & 76.52 & -13.94 & 194.32\\
\hline
86.6 & 76.52 & 10.08 & 101.61\\
\hline
80.05 & 76.52 & 3.53 & 12.46\\
\hline
77.42 & 76.52 & 0.9 & 0.81\\
\hline
Total &  &  & 957.29\\
\hline
\end{tabular}

#### Variance

The sum of squares helps quantify spread: the larger the sum of squares,
the greater the spread of observations around the population mean. There
is one issue with the sum of squares, though: since the sum of square is
derived from the differences between each observation and the mean, it
is also related to the number of individuals overall in our population.
In our example above, the sum of squares was 10.

Now, let's generate a dataset with two 1s, two 2s, two 3s, two 4s, and
two 5s:


```r
first_squares_table = first_resid_table %>%
  mutate(square = residual^2)

double_squares_table = first_squares_table %>%
  rbind(first_squares_table)

double_squares_totals = data.frame(individuals = "Total",
                                mean = "",
                                residual = "",
                                square = sum(double_squares_table$square))

double_squares_table %>%
  rbind(double_squares_totals) %>%
  kbl()
```


\begin{tabular}[t]{l|l|l|r}
\hline
individuals & mean & residual & square\\
\hline
1 & 3 & -2 & 4\\
\hline
2 & 3 & -1 & 1\\
\hline
3 & 3 & 0 & 0\\
\hline
4 & 3 & 1 & 1\\
\hline
5 & 3 & 2 & 4\\
\hline
1 & 3 & -2 & 4\\
\hline
2 & 3 & -1 & 1\\
\hline
3 & 3 & 0 & 0\\
\hline
4 & 3 & 1 & 1\\
\hline
5 & 3 & 2 & 4\\
\hline
Total &  &  & 20\\
\hline
\end{tabular}

You will notice the sum of squares increases to 20. The spread of the
data did not change: we recorded the same five values. The only
difference is that we observed each value twice.

The moral of this story is this: given any distribution, the sum of
squares will always increase with the number of observations. Thus, if
we want to compare the spread of two different populations with
different numbers of individuals, we need to adjust our interpretation
to allow for the number of observations.

We do this by dividing the sum of squares, $S_{xx}$ by the number of
observations, $n$. In essence, we are calculating an "average" of the
sum of squares. This value is the variance, $\sigma^2$.

$$\sigma^2 = \frac{S_{xx}}{n}$$ We can calculate the variance as
follows.

In our first example, the set {1,2,3,4,5} had a sum of squares of 10. in
that case, the variance would be:

$$\frac{10}{5} = 2
$$

In our second example, the set {1,2,3,4,5,1,2,3,4,5} had a sum of
squares of 20. In that example, the variance would be

$$
\frac{20}{10} = 2
$$

As you can see, the variance is not affected by the size of the dataset,
only by the distribution of its values.

Later on in this course, we will calculate the variance a little
differently:

$$\sigma^2 = \frac{S_{xx}}{n-1}$$ *n-1* is referred to as the **degrees
of freedom**. We use degrees of freedom when we work with samples
(subsets) of a population. In this unit, however, we are working with
populations, so we will not worry about those.

#### Standard Deviation

Our challenge in using the variance to describe population spread is
it's units are not intuitive. When we square the measure we also square
the units of measure. For example the variance of our yield is measured
in units of bushels^2^. Wrap your head around that one. Our solution is
to report our final estimate of population spread in the original units
of measure. To do this, we calculate the square root of the variance.
This statistic is called the standard deviation, $\sigma$.

$$\sigma = \sqrt{(\sigma^2)}$$

For the dataset {1,2,3,4,5}, the sum of squares is 10, the variance 2,
and the standard deviation is:

$$\sigma = \sqrt{2} = 1.4 $$

For our yield dataset, the sum of squares is 957.29, and based on 10
observations. Our variance is therefore:

$$\sigma^2 = \frac{957.29}{10} = 9.57 \text{ bushels}^2 \text{ / acre}^2$$

Our sum of squares is :

$$ \sqrt{9.57 \text{ bushels}^2 \text{ / acre}^2} = 3.09
\text{ bushels / acre }$$

That is enough theory for this first week of class. The remainder of
this lesson will focus on introducing you to **RStudioCloud**.
