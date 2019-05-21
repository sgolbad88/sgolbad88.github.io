---
title: "Statistics Project: Handwashing"
date: 2019-05-21
tags: [statistics, data science, t-test]
header:
  image: "/images/Semmelweis.jpg"
excerpt: "Statistics, Handwashing, Data Science"
---

# Meet Dr. Ignaz Semmelweis

Dr. Ignaz Semmelweis, a Hungarian physician born in 1818, was active at the Vienna General Hospital and thinking about childbed fever: A deadly disease affecting women that just have given birth in the early 1840s. At the Vienna General Hospital, as many as 10% of the women giving birth died from it. He is thinking about it because he knows the cause of childbed fever: It's the contaminated hands of the doctors delivering the babies. And they won't listen to him and wash their hands!

In this analysis, we're going to reanalyze the data that made Semmelweis discover the importance of handwashing. Let's start by looking at the data that made Semmelweis realize that something was wrong with the procedures at Vienna General Hospital.

R code block:
```r
# Load in the tidyverse package
library(tidyverse)

# Read datasets/yearly_deaths_by_clinic.csv into yearly
yearly <- read_csv("datasets/yearly_deaths_by_clinic.csv")

# Print out yearly
yearly
```
<img src="{{ site.url }}{{ site.baseurl }}/images/initialtable.jpg" alt="data">

# The alarming number of deaths

The table above shows the number of women giving birth at the two clinics at the Vienna General Hospital for the years 1841 to 1846. You'll notice that giving birth was very dangerous; an alarming number of women died as the result of childbirth, most of them from childbed fever.

We see this more clearly if we look at the *proportion of deaths* out of the number of women giving birth.

R code block:
```r
# Adding a new column to yearly with proportion of deaths per no. births
yearly <- yearly %>% mutate(proportion_deaths = deaths/births)

# Print out yearly
yearly
```
<img src="{{ site.url }}{{ site.baseurl }}/images/proportiondeathstable.jpg" alt="proportion of deaths data">

# Deaths at the clinic

If we now plot the proportion of deaths at both clinic 1 and clinic 2 we'll see a curious pattern...

R code block:
```r
# Setting the size of plots in this notebook
options(repr.plot.width=7, repr.plot.height=4)

# Plot yearly proportion of deaths at the two clinics
ggplot(yearly, aes(x = year, y = proportion_deaths, color = clinic)) +
    geom_line() +
    labs(x = "Year", y = "Proportion of Deaths")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/lineplotproportiondeaths.jpg" alt="proportion of deaths line plot">

# The handwashing begins

Why is the proportion of deaths constantly so much higher in Clinic 1? Semmelweis saw the same pattern and was puzzled and distressed. The only difference between the clinics was that many medical students served at Clinic 1, while mostly midwife students served at Clinic 2. While the midwives only tended to the women giving birth, the medical students also spent time in the autopsy rooms examining corpses.

Semmelweis started to suspect that something on the corpses, spread from the hands of the medical students, caused childbed fever. So in a desperate attempt to stop the high mortality rates, he decreed: *Wash your hands!* This was an unorthodox and controversial request, nobody in Vienna knew about bacteria at this point in time.

Let's load in monthly data from Clinic 1 to see if the handwashing had any effect.

R code block:
```r
# Read datasets/monthly_deaths.csv into monthly
monthly <- read_csv("datasets/monthly_deaths.csv")

# Adding a new column with proportion of deaths per no. births
monthly <- monthly %>% mutate(proportion_deaths = deaths/births)

# Print out the first rows in monthly
head(monthly)
```
<img src="{{ site.url }}{{ site.baseurl }}/images/handwashingtable.jpg" alt="handwashing data">

# The effect of handwashing

With the data loaded we can now look at the proportion of deaths over time. In the plot below we haven't marked where obligatory handwashing started, but it reduced the proportion of deaths to such a degree that you should be able to spot it!

R code block:
```r
# Plot monthly proportion of deaths
ggplot(monthly, aes(x = date, y = proportion_deaths)) +
    geom_line() +
    labs(x = "Year", y = "Proportion of Deaths")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/handwashinglineplot.png" alt="handwashing line plot">

# The effect of handwashing highlighted

Starting from the summer of 1847 the proportion of deaths is drastically reduced and, yes, this was when Semmelweis made handwashing obligatory.

The effect of handwashing is made even more clear if we highlight this in the graph.

R code block:
```r
# From this date handwashing was made mandatory
handwashing_start = as.Date('1847-06-01')

# Add a TRUE/FALSE column to monthly called handwashing_started
monthly <- monthly %>%
    mutate(handwashing_started =
           date >= handwashing_start)

# Plot monthly proportion of deaths before and after handwashing
ggplot(monthly, aes(x = date, y = proportion_deaths, color = handwashing_started)) +
    geom_line() +
    labs(x = "Year", y = "Proportion of Deaths")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/handwashinghighefflp.png" alt="handwashing effect highlighted line plot">

# More handwashing, fewer deaths?

Again, the graph shows that handwashing had a huge effect. How much did it reduce the monthly proportion of deaths on average?

R code block:
```r
# Calculating the mean proportion of deaths
# before and after handwashing.

monthly_summary <- monthly %>%
    group_by(handwashing_started) %>%
    summarize(mean_proportion_deaths = mean(proportion_deaths))

# Printing out the summary.
monthly_summary
```

<img src="{{ site.url }}{{ site.baseurl }}/images/meanpropdeathstable.jpg" alt="mean proportion of deaths data">

# A statistical analysis of Semmelweis handwashing data

It reduced the proportion of deaths by around 8 percentage points! From 10% on average before handwashing to just 2% when handwashing was enforced (which is still a high number by modern standards). To get a feeling for the uncertainty around how much handwashing reduces mortalities we could look at a confidence interval (here calculated using a t-test).

R code block:
```r
# Calculating a 95% Confidence intrerval using t.test
test_result <- t.test(proportion_deaths ~ handwashing_started, data = monthly)
test_result
```

<img src="{{ site.url }}{{ site.baseurl }}/images/handwashingttest.jpg" alt="handwashing t-test">

# The fate of Dr. Semmelweis
That the doctors didn't wash their hands increased the proportion of deaths by between 6.7 and 10 percentage points, according to a 95% confidence interval. All in all, it would seem that Semmelweis had solid evidence that handwashing was a simple but highly effective procedure that could save many lives.

The tragedy is that, despite the evidence, Semmelweis' theory — that childbed fever was caused by some "substance" (what we today know as bacteria) from autopsy room corpses — was ridiculed by contemporary scientists. The medical community largely rejected his discovery and in 1849 he was forced to leave the Vienna General Hospital for good.

One reason for this was that statistics and statistical arguments were uncommon in medical science in the 1800s. Semmelweis only published his data as long tables of raw data, but he didn't show any graphs nor confidence intervals. If he would have had access to the analysis we've just put together he might have been more successful in getting the Viennese doctors to wash their hands.

R code block:
```r
# The data Semmelweis collected points to that:
doctors_should_wash_their_hands <- TRUE
```
