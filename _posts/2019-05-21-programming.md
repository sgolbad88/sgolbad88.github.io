---
title: "Statistics Project: Programming"
date: 2019-05-22
tags: [statistics, data science, data visualization]
header:
excerpt: "Data Visualization, Programming, Data Science"
---

<img src="{{ site.url }}{{ site.baseurl }}/images/tags.png" alt="tags">

## Data on tags over time

How can we tell what programming languages and technologies are used by the most people? How about what languages are growing and which are shrinking, so that we can tell which are most worth investing time in?

One excellent source of data is Stack Overflow, a programming question and answer site with more than 16 million questions on programming topics. By measuring the number of questions about each technology, we can get an approximate sense of how many people are using it. We're going to use open data from the Stack Exchange Data Explorer to examine the relative popularity of languages like R, Python, Java and Javascript have changed over time.

Each Stack Overflow question has a tag, which marks a question to describe its topic or technology. For instance, there's a tag for languages like R or Python, and for packages like ggplot2 or pandas.

We'll be working with a dataset with one observation for each tag in each year. The dataset includes both the number of questions asked in that tag in that year, and the total number of questions asked in that year.

R code block:
```r
# Load libraries
library(readr)
library(dplyr)

# Load dataset
by_tag_year <- read_csv("datasets/by_tag_year.csv")
```
This data has one observation for each pair of a tag and a year, showing the number of questions asked in that tag in that year and the total number of questions asked in that year. For instance, there were 54 questions asked about the .htaccess tag in 2008, out of a total of 58390 questions in that year.

Rather than just the counts, we're probably interested in a percentage: the fraction of questions that year that have that tag. So let's add that to the table.

R code block:
```r
# Add fraction column
by_tag_year_fraction <- mutate(by_tag_year, fraction = number/year_total)
```

## Has R been growing or shrinking?

So far I've been learning and using the R programming language. I want to be sure it's a good investment for the future. Has it been keeping pace with other languages, or have people been switching out of it?

Let's look at whether the fraction of Stack Overflow questions that are about R has been increasing or decreasing over time.

R code block:
```r
# Filter for R tags
r_over_time <- filter(by_tag_year_fraction, tag == "r")
```

Rather than looking at the results in a table, we often want to create a visualization. Change over time is usually visualized with a line plot.

R code block:
```r
# Load ggplot2
library(ggplot2)

# Create a line plot of fraction over time
ggplot(r_over_time, aes(x = year, y = fraction)) +
    geom_line() +
    labs(title = "R Over Time", x = "Year", y = "Fraction")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/rovertime.png" alt="r overtime line plot">

## How about dplyr and ggplot2?

Based on that graph, it looks like R has been growing pretty fast in the last decade. Good thing we're practicing it now!

Besides R, two other interesting tags are dplyr and ggplot2, which we've already used in this analysis. They both also have Stack Overflow tags!

Instead of just looking at R, let's look at all three tags and their change over time. Are each of those tags increasing as a fraction of overall questions? Are any of them decreasing?

R code block:
```r
# A vector of selected tags
selected_tags <- c("r", "dplyr", "ggplot2" )

# Filter for those tags
selected_tags_over_time <- filter(by_tag_year_fraction, tag %in% selected_tags)

# Plot tags over time on a line plot using color to represent tag
ggplot(selected_tags_over_time, aes(x = year, y = fraction, color = tag)) +
    geom_line() +
    labs(title = "Over Time", x = "Year", y = "Fraction")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/overtime.png" alt="programming languages line plot">

## What are the most asked-about tags?
It's sure been fun to visualize and compare tags over time. The dplyr and ggplot2 tags may not have as many questions as R, but we can tell they're both growing quickly as well.

We might like to know which tags have the most questions overall, not just within a particular year. Right now, we have several rows for every tag, but we'll be combining them into one. That means we want group_by() and summarize().

Let's look at tags that have the most questions in history.

R code block:
```r
# Find total number of questions for each tag
sorted_tags <- by_tag_year %>%
group_by(tag) %>%
summarize(tag_total=sum(number)) %>%
arrange(desc(tag_total))
```

## How have large programming languages changed over time?

We've looked at selected tags like R, ggplot2, and dplyr, and seen that they're each growing. What tags might be shrinking? A good place to start is to plot the tags that we just saw that were the most-asked about of all time, including JavaScript, Java and C#.

R code block:
```r
# Get the six largest tags
highest_tags <- head(sorted_tags$tag)
​
# Filter for the six largest tags
by_tag_subset <- filter(by_tag_year_fraction, tag %in% highest_tags)
​
# Plot tags over time on a line plot using color to represent tag
ggplot(by_tag_subset, aes(x = year, y = fraction, color = tag)) +
    geom_line() +
    labs(title = "Over Time", x = "Year", y = "Fraction")
```

<img src="{{ site.url }}{{ site.baseurl }}/images/6overtime.png" alt="programming languages line plot">

Wow, based on that graph we've seen a lot of changes in what programming languages are most asked about. C# gets fewer questions than it used to, and Python has grown quite impressively.

This Stack Overflow data is incredibly versatile. We can analyze any programming language, web framework, or tool where we'd like to see their change over time. Combined with the reproducibility of R and its libraries, we have ourselves a powerful method of uncovering insights about technology.
