---
title       : "Data Manipulation Using dplyr"
subtitle    : "An Introduction"
author      : "Peter von Rohr"
job         : "Charlotte Team"
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## dplyr Installation

`dplyr` is an R package on CRAN and can be obtained and installed using the conventional R package installation mechanism

```
install.packages('dplyr')
```

should do the trick.


```r
library(dplyr)
```
loads and attaches the `dplyr` package

The following material is a summary of the introductory vignette in the `dplyr` packages which is available with 

```
browseVignettes(package = 'dplyr')
```

--- .class #id 

## Data Manipulation so far

- Traditionally, data manipulation in R is done via index selection on data.frames

- Data are read from external sources, such as files or databases or other objects into R

- Data in R are mostly stored in data.frames which are objects based on lists with additional matrix-like properties

- Subsets of data.frames are extracted using index-based selection. As an example the following statement extracts from the `iris` data.frame all rows which have `Sepal.Length > 5.5` and `Species` name "setosa"


```r
iris[iris$Sepal.Length > 5.5 & iris$Species == "setosa",]
```

```
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 15          5.8         4.0          1.2         0.2  setosa
## 16          5.7         4.4          1.5         0.4  setosa
## 19          5.7         3.8          1.7         0.3  setosa
```

---

## dplyr

- Data manipulation using `dplyr` is based on six verbs each performing a single task

1. filter() (and slice())
2. arrange()
3. distinct()
4. mutate() (and transmute())
5. summarise()
6. sample_n() and sample_frac()

- Usage of above functions are analogous
   - the first argument is a data frame
   - subsequent arguments describe what to do
   - result is a new data frame
- Operations can be pipelined using the `%>%`-operator

---

## filter()
- Selection of rows from a data frame based on expressions


```r
filter(iris, Sepal.Length > 5.6, Species == "setosa")
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.8         4.0          1.2         0.2  setosa
## 2          5.7         4.4          1.5         0.4  setosa
## 3          5.7         3.8          1.7         0.3  setosa
```

- Rows can also be selected by positions

```r
slice(iris, 50:51)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
## 1            5         3.3          1.4         0.2     setosa
## 2            7         3.2          4.7         1.4 versicolor
```

---

## arrange()
- Reordering of rows by sorting them according to specified columns

```r
head(arrange(iris, Petal.Width, Petal.Length), 2)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          4.3           3          1.1         0.1  setosa
## 2          4.8           3          1.4         0.1  setosa
```

- Use `desc()` for sorting in descending order

```r
head(arrange(iris, desc(Petal.Width)), 3)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1          6.3         3.3          6.0         2.5 virginica
## 2          7.2         3.6          6.1         2.5 virginica
## 3          6.7         3.3          5.7         2.5 virginica
```

---

## select()
- Selection of a subset of columns

```r
head(select(iris,Sepal.Length,Petal.Length), 3)
```

```
##   Sepal.Length Petal.Length
## 1          5.1          1.4
## 2          4.9          1.4
## 3          4.7          1.3
```

- Range of columns

```r
head(select(iris, Sepal.Width:Petal.Width), 2)
```

```
##   Sepal.Width Petal.Length Petal.Width
## 1         3.5          1.4         0.2
## 2         3.0          1.4         0.2
```

---

## select() 
- all columns except the ones specified

```r
head(select(iris, -(Sepal.Width:Petal.Width)), 2)
```

```
##   Sepal.Length Species
## 1          5.1  setosa
## 2          4.9  setosa
```

- renaming columns using `select()`

```r
head(select(iris, sepal_len = Sepal.Length), 2)
```

```
##   sepal_len
## 1       5.1
## 2       4.9
```

---

## rename()
changing column names and keeping the rest

```r
head(rename(iris, sepal_len = Sepal.Length), 5)
```

```
##   sepal_len Sepal.Width Petal.Length Petal.Width Species
## 1       5.1         3.5          1.4         0.2  setosa
## 2       4.9         3.0          1.4         0.2  setosa
## 3       4.7         3.2          1.3         0.2  setosa
## 4       4.6         3.1          1.5         0.2  setosa
## 5       5.0         3.6          1.4         0.2  setosa
```

---

## distinct()
selection of unique rows 

```r
distinct(select(iris, Species))
```

```
##      Species
## 1     setosa
## 2 versicolor
## 3  virginica
```

---

## mutate()
- adding new columns

```r
head(mutate(iris, len_rat = Petal.Length / Sepal.Length, lr2 = len_rat^2), 3)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species len_rat
## 1          5.1         3.5          1.4         0.2  setosa  0.2745
## 2          4.9         3.0          1.4         0.2  setosa  0.2857
## 3          4.7         3.2          1.3         0.2  setosa  0.2766
##       lr2
## 1 0.07536
## 2 0.08163
## 3 0.07651
```

- in contrast to `base::transform()` newly created columns can immediately be used with `dplyr::mutate()`

---

## summarise()
Collapsing a data frame into a single row, using a aggregation function such as `mean`

```r
summarise(iris, pet_wi = mean(Petal.Width, na.rm = TRUE))
```

```
##   pet_wi
## 1  1.199
```

---

## sample_n(), sample_frac()
- Drawing a random sample of rows of either a fixed number (`sample_n()`) or of a fixed fraction (`sample_frac()`)

```r
sample_n(iris, 5)
```

```
##    Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
## 16          5.7         4.4          1.5         0.4     setosa
## 78          6.7         3.0          5.0         1.7 versicolor
## 18          5.1         3.5          1.4         0.3     setosa
## 11          5.4         3.7          1.5         0.2     setosa
## 8           5.0         3.4          1.5         0.2     setosa
```

- Use option `replace = TRUE` to perform a bootstrap sample

- Optionally use the `weight` argument to do weighted sampling

---

## Grouped operations

- While single verbs are useful, they get really powerful when applying them on groups of data

- The following example shows how to get average sepal length and petal length for each iris species


```r
irisSpecies <- group_by(iris,Species)
irisLength <- summarize(irisSpecies, 
                        count = n(), 
                        sep_len = mean(Sepal.Length, na.rm = TRUE), 
                        pet_len = mean(Petal.Length, na.rm = TRUE))
irisLength
```

```
## Source: local data frame [3 x 4]
## 
##      Species count sep_len pet_len
## 1     setosa    50   5.006   1.462
## 2 versicolor    50   5.936   4.260
## 3  virginica    50   6.588   5.552
```

---

## Aggregation functions
- An aggregation function takes a vector of values and returns a single result
- In addition to base R's aggregation function such as `min()`, `max()`, `mean()`, `sum()`, `median()` and `IQR()`, dplyr provides some more like
   - `n()`: count the number of elements in a group
   - `n_distinct()`: count the number of unique elements
   - `first(x)`, `last(x)`, `n_th(x,n)`: work similar to `x[1]`, `x[length(x)]` and `x[n]`
- one can also use custom aggregation functions

---

## Chaining
`dplyr` functions do not have side-effects, hence results must always be saved after each step

```r
step1 <- group_by(iris, Species)
step2 <- select(step1, Sepal.Width, Petal.Width)
(step3 <- summarise(step2, 
                   sep_wi = mean(Sepal.Width, na.rm = TRUE), 
                   pet_wi = mean(Petal.Width, na.rm = TRUE)))
```

```
## Source: local data frame [3 x 3]
## 
##      Species sep_wi pet_wi
## 1     setosa  3.428  0.246
## 2 versicolor  2.770  1.326
## 3  virginica  2.974  2.026
```

---
  
## Pipeline
Saving intermediate results can be avoided by setting up a pipeline using `%>%`

```r
iris %>% 
  group_by(Species) %>% 
  select(Sepal.Width, Petal.Width) %>% 
  summarise(
     sep_wi = mean(Sepal.Width, na.rm = TRUE), 
     pet_wi = mean(Petal.Width, na.rm = TRUE))
```

```
## Source: local data frame [3 x 3]
## 
##      Species sep_wi pet_wi
## 1     setosa  3.428  0.246
## 2 versicolor  2.770  1.326
## 3  virginica  2.974  2.026
```
