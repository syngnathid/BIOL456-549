
# Producing Reports With `knitr`

## Context

Data analysts/Computational Biologists tend to write a lot of reports, describing their analyses and results, for their collaborators or to document their work for future reference.

When I was first starting out, I’d write an R script with all of my work, and would just send an email to my collaborator, describing the results and attaching various graphs. In discussing the results, there would often be confusion about which graph was which.

I moved to writing formal reports, with Word or LaTeX, but I’d have to spend a lot of time getting the figures to look right. Mostly, the concern is about page breaks.

Everything is easier now that I create a web page (as an html file). It can be one long stream, so I can use tall figures that wouldn’t ordinary fit on one page. Scrolling is your friend.

## Literate programming

Ideally, such analysis reports are reproducible documents: If an error is discovered, or if some additional subjects are added to the data, you can just re-compile the report and get the new or corrected results (versus having to reconstruct figures, paste them into a Word document, and further hand-edit various detailed results).

The key tool for R is knitr, which allows you to create a document that is a mixture of text and some chunks of code. When the document is processed by knitr, chunks of R code will be executed, and graphs or other results inserted.

This sort of idea has been called “literate programming”.

knitr allows you to mix basically any sort of text with any sort of code, but we recommend that you use R Markdown, which mixes Markdown with R. Markdown is a light-weight mark-up language for creating web pages.

## Creating an R Markdown file

Within R Studio, click File → New File → R Markdown and you’ll get a dialog box like this:

You can stick with the default (HTML output), but give it a title.

```r
---
title: "Initial R Markdown document"
author: "Balan Ramesh"
date: "2022-Feb-22"
output: html_document
---
```

You can delete any of those fields if you don’t want them included. The double-quotes aren’t strictly necessary in this case. They’re mostly needed if you want to include a colon in the title.

RStudio creates the document with some example text to get you started. Note below that there are chunks like

```r
summary(cars)
```
These are chunks of R code that will be executed by knitr and replaced by their results. More on this later.

Also note the web address that’s put between angle brackets (`< >`) as well as the double-asterisks in **Knit**. This is Markdown.

## Markdown

Markdown is a system for writing web pages by marking up the text much as you would in an email rather than writing html code. The marked-up text gets converted to html, replacing the marks with the proper html code.

For now, let’s delete all of the stuff that’s there and write a bit of markdown.

You make things bold using two asterisks, like this: **bold**, and you make things italics by using underscores, like this: _italics_.

You can make a bulleted list by writing a list with hyphens or asterisks, like this:

```
* bold with double-asterisks
* italics with single-asterisks
* code-type font with backticks
```

```
- bold with double-asterisks
- italics with single-asterisks
- code-type font with backticks
```

Each will appear as:

- bold with double-asterisks
- italics with underscores
- code-type font with backticks
(I prefer hyphens over asterisks, myself.)

You can make a numbered list by just using numbers. You can use the same number over and over if you want:

```
1. bold with double-asterisks
1. italics with underscores
1. code-type font with backticks
```
This will appear as:

1. bold with double-asterisks
1. italics with underscores
1. code-type font with backticks

You can make section headers of different sizes by initiating a line with some number of `#` symbols:

```
# Title
## Main section
### Sub-section
#### Sub-sub section
```

You can make a hyperlink like this: [text to show](http://the-web-page.com).

You can include an image file like this: ![caption](https://pipefishguysite.files.wordpress.com/2020/09/field_trip.jpg)

You can do subscripts (e.g., `F~2~`) with F~2~ and superscripts (e.g., `F^2^`) with F^2^. 

If you know how to write equations in LaTeX, you’ll be glad to know that you can use `$ $` and `$$ $$` to insert math equations, like `$E = mc^2$` and

> $$y = \mu + \sum_{i=1}^p \beta_i x_i + \epsilon$$

$$y = \mu + \sum_{i=1}^p \beta_i x_i + \epsilon$$


## R code chunks

Markdown is interesting and useful, but the real power comes from mixing markdown with chunks of R code. This is R Markdown. When processed, the R code will be executed; if they produce figures, the figures will be inserted in the final document.

The main code chunks look like this:

```r
variants <- read.csv("/mnt/ceph/ajones_csb/r_data-manipulation/combined_tidy_vcf.csv")
ggplot(data = variants, aes(x = POS, y = DP)) +
geom_point()
```

That is, you place a chunk of R code between ```{r chunk_name} and ```. It’s a good idea to give each chunk a name, as they will help you to fix errors and, if any graphs are produced, the file names are based on the name of the code chunk that produced them.

### How things get compiled


When you press the “Knit HTML” button, the R Markdown document is processed by knitr and a plain Markdown document is produced (as well as, potentially, a set of figure files): the R code is executed and replaced by both the input and the output; if figures are produced, links to those figures are included.

The Markdown and figure documents are then processed by the tool pandoc, which converts the Markdown file into an html file, with the figures embedded.

![](https://datacarpentry.org/genomics-r-intro/fig/rmd-07-rmd_to_html_fig-1.png)

### Chunk options
There are a variety of options to affect how the code chunks are treated.

Use `echo = FALSE` to avoid having the code itself shown.
Use `results = "hide"` to avoid having any results printed.
Use `eval = FALSE` to have the code shown but not evaluated.
Use `warning = FALSE` and `message = FALSE` to hide any warnings or messages produced.
Use `fig.height` and `fig.width` to control the size of the figures produced (in inches).


```r
{r plotVariants, echo=FALSE, message=FALSE, fig.width=11}
variants <- read.csv("/mnt/ceph/ajones_csb/r_data-manipulation/combined_tidy_vcf.csv")
ggplot(data = variants, aes(x = POS, y = DP)) +
geom_point()
```

### Run Other Languages.

Besides the R language, many other languages are supported in R Markdown through the knitr package. The language name is indicated by the first word in the curly braces after the three opening backticks. For example, the little r in ```{r} indicates that the code chunk contains R code, and ```{python} is a Python code chunk. In this chapter, we show a few languages that you may not be familiar with.

In knitr, each language is supported through a language engine. Language engines are essentially functions that take the source code and options of a chunk as the input, and return a character string as the output. They are managed through the object `knitr::knit_engines`. You may check the existing engines via:

```r
names(knitr::knit_engines$get())
```

```r
{python}
print("Hello Python!")
```

```r
{bash}
ls -l .
```

At the moment, most code chunks of non-R languages are executed independently. For example, all bash code chunks in the same document are executed separately in their own sessions, so a later bash code chunk cannot use variables created in a previous bash chunk, and the changed working directory (via cd) will not be persistent across different bash chunks. Only R, Python, and Julia code chunks are executed in the same session. Please note that all R code chunks are executed in the same R session, and all Python code chunks are executed in the same Python session, etc. The R session and the Python session are two different sessions, but it is possible to access or manipulate objects of one session from another session (see [here](https://bookdown.org/yihui/rmarkdown-cookbook/eng-python.html#eng-python)).

## Resources:

1. [Much of this lesson was copied or adapted from Data Carpentry materials](https://datacarpentry.org/genomics-r-intro/07-knitr-markdown/index.html)
2. [Rmarkdown Cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/)
3. [R for Reproducible Scientific Analysis](https://swcarpentry.github.io/r-novice-gapminder/)
4. [Introduction to Open Data Science](http://ohi-science.org/data-science-training/)
5. [Reproducible Research Techniques for Synthesis](https://learning.nceas.ucsb.edu/2019-11-RRCourse/index.html)
6. [Ten simple rules for biologists learning to program](https://doi.org/10.1371/journal.pcbi.1005871)
