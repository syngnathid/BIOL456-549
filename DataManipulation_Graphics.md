---
layout: page
title: Data Manipulation using tidyr and dplyr
nav_exclude: true
---

## Data Manipulation using `tidyr` and `dplyr`


A substantial amount of the data we work with in genomics will be tabular data, this is data arranged in rows and columns - also known as spreadsheets. We could write a whole lesson on how to work with spreadsheets effectively. For our purposes, we want to remind you of a few principles before we work with our first set of example data:

1) Keep raw data separate from analyzed data

This is principle number one because if you can’t tell which files are the original raw data, you risk making some serious mistakes (e.g. drawing conclusion from data which have been manipulated in some unknown way).

2) Keep spreadsheet data Tidy

The simplest principle of Tidy data is that we have one row in our spreadsheet for each observation or sample, and one column for every variable that we measure or report on. As simple as this sounds, it’s very easily violated. Most data scientists agree that significant amounts of their time is spent tidying data for analysis. Read more about data organization in this [lesson](https://datacarpentry.org/organization-genomics/) and in this [paper](https://www.jstatsoft.org/article/view/v059i10).

3) Trust but verify

Finally, while you don’t need to be paranoid about data, you should have a plan for how you will prepare it for analysis. This a focus of this lesson. You probably already have a lot of intuition, expectations, assumptions about your data - the range of values you expect, how many values should have been recorded, etc. Of course, as the data get larger our human ability to keep track will start to fail (and yes, it can fail for small data sets too). R will help you to examine your data so that you can have greater confidence in your analysis, and its reproducibility.

> When you work with data in R, you are not changing the original file you loaded that data from. This is different than (for example) working with a spreadsheet program where changing the value of the cell leaves you one “save”-click away from overwriting the original file. You have to purposely use a writing function (e.g. write.csv()) to save data loaded into R. In that case, be sure to save the manipulated data into a new file. More on this later in the lesson.

## Importing tabular data into R

There are several ways to import data into R. For our purpose here, we will focus on using the tools every R installation comes with (so called “base” R) to import a comma-delimited file containing the results of our variant calling workflow. We will need to load the sheet using a function called `read.csv()`.

Now, let’s read in the file `combined_tidy_vcf.csv` which will be located in /mnt/ceph/ajones_csb/r_data-manipulation/. Call this data `variants`. The first argument to pass to our `read.csv()` function is the file path for our data. The file path must be in quotes and now is a good time to remember to use tab autocompletion. If you use tab autocompletion you avoid typos and errors in file paths. Use it!


```r
## read in a CSV file and save it as 'variants'

variants <- read.csv("/mnt/ceph/ajones_csb/r_data-manipulation/combined_tidy_vcf.csv")
```

One of the first things you should notice is that in the Environment window, you have the `variants` object, listed as 801 obs. (observations/rows) of 29 variables (columns). Double-clicking on the name of the object will open a view of the data in a new tab

## Data Wrangling and Analyses with Tidyverse

The `dplyr` package provides a number of very useful functions for manipulating data frames in a way that will reduce repetition, reduce the probability of making errors, and probably even save you some typing. As an added bonus, you might even find the `dplyr` grammar easier to read.

Here we’re going to cover some of the most commonly used functions as well as using pipes (`%>%`) to combine them:

1. `glimpse()`
2. `select()`
3. `filter()`
4. `group_by()`
5. `summarize()`
6. `mutate()`
7. `pivot_longer` and `pivot_wider`

Packages in R are sets of additional functions that let you do more stuff in R. Packages give you access to more functions. You need to install a package and then load it to be able to use it.

```r
install.packages("dplyr") ## install
```

You might get asked to choose a CRAN mirror – this is asking you to choose a site to download the package from. The choice doesn’t matter too much; I’d recommend choosing the RStudio mirror.

```r
library("dplyr")          ## load
```
You only need to install a package once per computer, but you need to **load it every time you open a new R session** and want to use that package.

## What is dplyr?

The package `dplyr` is a fairly new (2014) package that tries to provide easy tools for the most common data manipulation tasks. This package is also included in the `tidyverse` package, which is a collection of eight different packages (`dplyr`, `ggplot2`, `tibble`, `tidyr`, `readr`, `purrr`, `stringr`, and `forcats`). It is built to work directly with data frames. The thinking behind it was largely inspired by the package `plyr` which has been in use for some time but suffered from being slow in some cases. `dplyr` addresses this by porting much of the computation to C++. An additional feature is the ability to work with data stored directly in an external database. The benefits of doing this are that the data can be managed natively in a relational database, queries can be conducted on that database, and only the results of the query returned.

This addresses a common problem with R in that all operations are conducted in memory and thus the amount of data you can work with is limited by available memory. The database connections essentially remove that limitation in that you can have a database that is over 100s of GB, conduct queries on it directly and pull back just what you need for analysis in R.

### Taking a quick look at data frames

  `glimpse()` is a `dplyr` function that (as the name suggests) gives a glimpse of the data frame.

```r
variants %>%
glimpse()
```

```r
Rows: 801
Columns: 29
$ sample_id     <fct> SRR2584863, SRR2584863, SRR2584863, SRR2584863, SRR2584863, SRR2584863, SRR2584863, SRR2584863, SRR2584863, …
$ CHROM         <fct> CP000819.1, CP000819.1, CP000819.1, CP000819.1, CP000819.1, CP000819.1, CP000819.1, CP000819.1, CP000819.1, …
$ POS           <int> 9972, 263235, 281923, 433359, 473901, 648692, 1331794, 1733343, 2103887, 2333538, 2407766, 2446984, 2618472,…
$ ID            <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
$ REF           <fct> T, G, G, CTTTTTTT, CCGC, C, C, G, ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG, AT, A, A, G, A, G, A, C, A, A, A, G, A, …
$ ALT           <fct> G, T, T, CTTTTTTTT, CCGCGC, T, A, A, ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG, ATT, C, C, T,…
$ QUAL          <dbl> 91.0000, 85.0000, 217.0000, 64.0000, 228.0000, 210.0000, 178.0000, 225.0000, 56.0000, 167.0000, 104.0000, 22…
$ FILTER        <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
$ INDEL         <lgl> FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, …
$ IDV           <int> NA, NA, NA, 12, 9, NA, NA, NA, 2, 7, NA, NA, NA, NA, NA, NA, NA, NA, NA, 2, NA, NA, NA, 10, NA, NA, NA, NA, …
$ IMF           <dbl> NA, NA, NA, 1.000000, 0.900000, NA, NA, NA, 0.666667, 1.000000, NA, NA, NA, NA, NA, NA, NA, NA, NA, 1.000000…
$ DP            <int> 4, 6, 10, 12, 10, 10, 8, 11, 3, 7, 9, 20, 12, 19, 15, 10, 14, 9, 13, 2, 10, 16, 11, 10, 9, 9, 13, 14, 10, 11…
$ VDB           <dbl> 0.0257451, 0.0961330, 0.7740830, 0.4777040, 0.6595050, 0.2680140, 0.6240780, 0.9924030, 0.9016520, 0.5681730…
$ RPB           <dbl> NA, 1.000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.900802, NA, 0.954207, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
$ MQB           <dbl> NA, 1.0000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.1501340, NA, 0.0497871, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ BQB           <dbl> NA, 1.000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.750668, NA, 0.774755, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
$ MQSB          <dbl> NA, NA, 0.974597, 1.000000, 0.916482, 0.916482, 0.900802, 1.007750, 1.000000, 1.012830, 0.500000, 1.000000, …
$ SGB           <dbl> -0.556411, -0.590765, -0.662043, -0.676189, -0.662043, -0.670168, -0.651104, -0.670168, -0.453602, -0.616816…
$ MQ0F          <dbl> 0.000000, 0.166667, 0.000000, 0.000000, 0.000000, 0.000000, 0.000000, 0.000000, 0.000000, 0.000000, 0.333333…
$ ICB           <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
$ HOB           <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
$ AC            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
$ AN            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
$ DP4           <fct> "0,0,0,4", "0,1,0,5", "0,0,4,5", "0,1,3,8", "1,0,2,7", "0,0,7,3", "0,0,3,5", "0,0,4,6", "0,1,1,1", "0,1,3,3"…
$ MQ            <int> 60, 33, 60, 60, 60, 60, 60, 60, 60, 60, 25, 60, 10, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, …
$ Indiv         <fct> /home/dcuser/dc_workshop/results/bam/SRR2584863.aligned.sorted.bam, /home/dcuser/dc_workshop/results/bam/SRR…
$ gt_PL         <fct> "121,0", "112,0", "247,0", "91,0", "255,0", "240,0", "208,0", "255,0", "111,28", "194,0", "131,0", "255,0", …
$ gt_GT         <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
$ gt_GT_alleles <fct> G, T, T, CTTTTTTTT, CCGCGC, T, A, A, ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG, ATT, C, C, T,…
```
In the above output, we can already gather some information about `variants`, such as the number of rows and columns, column names, type of vector in the columns, and the first few entries of each column.

### Selecting columns and filtering rows

To select columns of a data frame, use `select()`. The first argument to this function is the data frame (`variants`), and the subsequent arguments are the columns to keep.


```r
variants %>%
select(sample_id, REF, ALT, DP)
```

```r
     sample_id                              REF                                                      ALT DP
1   SRR2584863                                T                                                        G  4
2   SRR2584863                                G                                                        T  6
3   SRR2584863                                G                                                        T 10
4   SRR2584863                         CTTTTTTT                                                CTTTTTTTT 12
5   SRR2584863                             CCGC                                                   CCGCGC 10
6   SRR2584863                                C                                                        T 10
7   SRR2584863                                C                                                        A  8
8   SRR2584863                                G                                                        A 11
9   SRR2584863 ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG  3
10  SRR2584863                               AT                                                      ATT  7
...
```

To select all columns except certain ones, put a “-“ in front of the variable to exclude it.

```r
variants %>%
select(-CHROM)
```

`dplyr` also provides useful functions to select columns based on their names. For instance, `ends_with()` allows you to select columns that ends with specific letters. For instance, if you wanted to select columns that end with the letter “B”:

```r
variants %>%
select(ends_with("B"))
```

To choose rows, use `filter()`

```r
variants %>%
filter(sample_id == "SRR2584863")
```

`filter()` will keep all the rows that match the conditions that are provided. Here are a few examples:

```r
# rows for which the reference genome has T or G
variants %>%
filter(REF %in% c("T", "G"))
```

```r
# rows with QUAL values greater than or equal to 100
variants %>%
filter(QUAL >= 100)

```

```r
# rows that have TRUE in the column INDEL
variants %>%
filter(INDEL)
```


```r
# rows that don't have missing data in the IDV column
variants %>%
filter(!is.na(IDV))
```

`filter()` allows you to combine multiple conditions. You can separate them using a `,` as arguments to the function, they will be combined using the `&` (AND) logical operator. If you need to use the `|` (OR) logical operator, you can specify it explicitly:

```r
# this is equivalent to:
#   filter(variants, sample_id == "SRR2584863" & QUAL >= 100)
variants %>%
filter(sample_id == "SRR2584863", QUAL >= 100)
```

```r
variants %>%
filter(sample_id == "SRR2584863", (INDEL | QUAL >= 100))
```


### Pipes

But what if you wanted to `select` and `filter`? We can do this with pipes. Pipes, are a fairly recent addition to R. Pipes let you take the output of one function and send it directly to the next, which is useful when you need to many things to the same data set. It was possible to do this before pipes were added to R, but it was much messier and more difficult. Pipes in R look like `%>%` and are made available via the `magrittr` package, which is installed as part of `dplyr`. If you use RStudio, you can type the pipe with `Ctrl + Shift + M` if you’re using a PC, or `Cmd + Shift + M` if you’re using a Mac.

```r
variants %>%
  filter(sample_id == "SRR2584863") %>%
  select(REF, ALT, DP)
```

In the above code, we use the pipe to send the `variants` dataset first through `filter()`, to keep rows where `sample_id` matches a particular sample, and then through `select()` to keep only the `REF`, `ALT`, and `DP` columns. Since `%>%` takes the object on its left and passes it as the first argument to the function on its right, we don’t need to explicitly include the data frame as an argument to the `filter()` and `select()` functions any more.

Some may find it helpful to read the pipe like the word “then”. For instance, in the above example, we took the data frame variants, then we filtered for rows where sample_id was SRR2584863, then we selected the REF, ALT, and DP columns, then we showed only the first six rows. The dplyr functions by themselves are somewhat simple, but by combining them into linear workflows with the pipe, we can accomplish more complex manipulations of data frames.

If we want to create a new object with this smaller version of the data we can do so by assigning it a new name:

```r
SRR2584863_variants <- variants %>%
  filter(sample_id == "SRR2584863") %>%
  select(REF, ALT, DP)
```

This new object includes all of the data from this sample. Let’s look at just the first six rows to confirm it’s what we want:

```r
SRR2584863_variants
```

Similar to `head()` and `tail()` functions in bash, we can also look at the first or last six rows using tidyverse function `slice()`. Slice is a more versatile function that allows users to specify a range to view:

```r
SRR2584863_variants %>% head()
SRR2584863_variants %>% tail()
SRR2584863_variants %>% slice(1:6)
SRR2584863_variants %>% slice(10:25)
```

### Mutate

Frequently you’ll want to create new columns based on the values in existing columns, for example to do unit conversions or find the ratio of values in two columns. For this we’ll use the `dplyr` function `mutate()`.

We have a column titled “QUAL”. This is a Phred-scaled confidence score that a polymorphism exists at this position given the sequencing data. Lower QUAL scores indicate low probability of a polymorphism existing at that site. We can convert the confidence value QUAL to a probability value according to the formula `Probability = 1- 10 ^ -(QUAL/10)`:

\(Probability = 1- 10^ -(QUAL/10)\).

Let’s add a column (`POLPROB`) to our `variants` data frame that shows the probability of a polymorphism at that site given the data.

```r
variants %>%
  mutate(POLPROB = 1 - (10 ^ -(QUAL/10)))
```

### group_by() and summarize() functions

Many data analysis tasks can be approached using the “split-apply-combine” paradigm: split the data into groups, apply some analysis to each group, and then combine the results. `dplyr` makes this very easy through the use of the `group_by()` function, which splits the data into groups. When the data is grouped in this way `summarize()` can be used to collapse each group into a single-row summary. `summarize()` does this by applying an aggregating or summary function to each group. For example, if we wanted to group by sample_id and find the number of rows of data for each sample, we would do:

```r
variants %>%
  group_by(sample_id) %>%
  summarize(n())
```

It can be a bit tricky at first, but we can imagine physically splitting the data frame by groups and applying a certain function to summarize the data.

![](https://datacarpentry.org/genomics-r-intro/fig/split_apply_combine.png)

^The figure was adapted from the Software Carpentry lesson, [R for Reproducible Scientific Analysis](https://swcarpentry.github.io/r-novice-gapminder/13-dplyr/)

Here the summary function used was `n()` to find the count for each group. Since this is a quite a common operation, there is a simpler method called `tally()`:

```r
variants %>%
  group_by(ALT) %>%
  tally()
```

To show that there are many ways to achieve the same results, there is another way to approach this, which bypasses `group_by()` using the function `count()`:

```r
variants %>%
  count(ALT)
```

We can also apply many other functions to individual columns to get other summary statistics. For example,we can use built-in functions like `mean()`, `median()`, `min()`, and `max()`. These are called “built-in functions” because they come with R and don’t require that you install any additional packages. By default, all R functions operating on vectors that contains missing data will return `NA`. It’s a way to make sure that users know they have missing data, and make a conscious decision on how to deal with it. When dealing with simple statistics like the mean, the easiest way to ignore `NA` (the missing data) is to use `na.rm` = `TRUE` (rm stands for remove).

So to view the mean, median, maximum, and minimum filtered depth (`DP`) for each sample:

```r
variants %>%
  group_by(sample_id) %>%
  summarize(
    mean_DP = mean(DP),
    median_DP = median(DP),
    min_DP = min(DP),
    max_DP = max(DP))
```

### Reshaping data frames


It can sometimes be useful to transform the “long” tidy format, into the wide format. This transformation can be done with the `pivot_wider()` function provided by the `tidyr` package (also part of the `tidyverse`).

`pivot_wider()` takes a data frame as the first argument, and two arguments: the column name that will become the columns and the column name that will become the cells in the wide data.

```r
variants_wide <- variants %>%
  group_by(sample_id, CHROM) %>%
  summarize(mean_DP = mean(DP)) %>%
  pivot_wider(names_from = sample_id, values_from = mean_DP)
```

```r
variants_wide
```

The opposite operation of `pivot_wider()` is taken care by `pivot_longer()`. We specify the names of the new columns, and here add -CHROM as this column shouldn’t be affected by the reshaping:

```r
variants_wide %>%
  pivot_longer(-CHROM, names_to = "sample_id", values_to = "mean_DP")
```

## Resources:

1. [dplyr](https://swcarpentry.github.io/r-novice-gapminder/13-dplyr/index.html)
2. [tidyr](https://swcarpentry.github.io/r-novice-gapminder/14-tidyr/index.html)
3. [Much of this lesson was copied or adapted from Data Carpentry materials](https://datacarpentry.org/genomics-r-intro/05-dplyr/index.html#what-is-dplyr)