## Visualization using `ggplot2`


#### Introduction
R is one of the most powerful programming language for visualizing data. It is often used to generate publication-quality graphics. Today, we will give you a flavor of some of the quality plotting you can produce using the `R` package [`ggplot2`](http://ggplot2.tidyverse.org/). `ggplot2` is perhaps the best package for intuitive generation of high-quality figures. By the end of this lesson you will know the basic principles necessary to produce similar, static versions of many of the example plots you are looking at. 


#### Objectives
- To be able to use `ggplot2` to generate publication-quality graphics.
- To understand the basic *grammar of graphics*, including the aesthetics and geometry layers, adding statistics, transforming scales, and coloring or panelling by groups.
#### Keypoints
- Use `ggplot2` to create plots.
- Think about graphics in layers: aesthetics, geometry, statistics, scale transformation, and grouping.


#### Background

Plotting our data is one of the best ways to quickly explore it and the various relationships between variables.

There are three main plotting systems in R, the [base plotting system][base], the [lattice][lattice] package, and the [ggplot2][ggplot2] package.

[base]: https://www.rdocumentation.org/packages/graphics/versions/3.6.1
[lattice]: http://www.statmethods.net/advgraphs/trellis.html
[ggplot2]: http://docs.ggplot2.org/current/

Today we'll be learning about the `ggplot2` package, because it is the most effective for creating publication-quality graphics.

`ggplot2` is built on the *grammar of graphics*, the idea that any plot can be generated from the same set of components: a **data** set, a **coordinate system**, and a set of **geoms** (the visual representation of data points).

The key to understanding `ggplot2` is thinking about a figure in layers. This idea may be familiar to you if you have used image editing programs like Photoshop, Illustrator, or Inkscape.
[Main components of ggplot](https://darencard.net/public_resources/R/ggplot_intro_slides.pdf)

**`ggplot2`** is a plotting package that makes it simple to create complex plots from data in a data frame. It provides a more programmatic interface for specifying what variables to plot, how they are displayed, and general visual properties. Therefore, we only need minimal changes if the underlying data change or if we decide to change from a bar plot to a scatter plot. This helps in creating publication quality plots with minimal amounts of adjustments and tweaking.

**`ggplot2`** functions like data in the ‘long’ format, i.e., a column for every dimension, and a row for every observation. Well-structured data will save you lots of time when making figures with **`ggplot2`**

ggplot graphics are built step by step by adding new elements. Adding layers in this fashion allows for extensive flexibility and customization of plots.

#### Quickstart

Remember that in order to work with `ggplot2` package, we need to load it into R.
```r
#load package into R
library(ggplot2)
```

To build a ggplot, we will use the following basic template that can be used for different types of plots:

```r
ggplot(data = <DATA>, mapping = aes(<MAPPINGS>)) +  <GEOM_FUNCTION>()
```

Now let's load our **variants** dataset into `R`.

```r
variants <- read.csv("/mnt/ceph/ajones_csb/r_data-manipulation/combined_tidy_vcf.csv")

```

1. Use the ggplot() function and bind the plot to a specific data frame using the data argument

```r
ggplot(data = variants)
```

2. Define a mapping (using the aesthetic (aes) function), by selecting the variables to be plotted and specifying how to present them in the graph, e.g. as x/y positions or characteristics such as size, shape, color, etc.

```r
ggplot(data = variants, aes(x = POS, y = DP))
```

3. Add ‘geoms’ – graphical representations of the data in the plot (points, lines, bars). ggplot2 offers many different geoms; we will use some common ones today, including:

> * `geom_point()` for scatter plots, dot plots, etc.
> * `geom_boxplot()` for, well, boxplots!
> * `geom_line()` for trend lines, time series, etc.  

3a. To add a geom to the plot use the + operator. Because we have two continuous variables, let’s use geom_point() first:

```r
ggplot(data = variants, aes(x = POS, y = DP)) +
geom_point()
```

![](../assets/content/plot1.png)

## Resources

1. [ggplot2](https://swcarpentry.github.io/r-novice-gapminder/08-plot-ggplot2/index.html)
1. [R for Datascience](https://r4ds.had.co.nz/)
2. [Intro to R and Rstudio - Datacarpentry](https://datacarpentry.org/genomics-r-intro/)
3. [R for Reproducible Scientific Analysis](https://swcarpentry.github.io/r-novice-gapminder/)
4. [Introduction to Open Data Science](http://ohi-science.org/data-science-training/)
5. [Reproducible Research Techniques for Synthesis](https://learning.nceas.ucsb.edu/2019-11-RRCourse/index.html)
6. [Ten simple rules for biologists learning to program](https://doi.org/10.1371/journal.pcbi.1005871)
7. [Ten quick tips for delivering programming lessons](https://doi.org/10.1371/journal.pcbi.1007433)

