Tibbles
================
Samantha Toet
8/29/2018

Intro
-----

From [10. Tibbles](http://r4ds.had.co.nz/tibbles.html)

*Ch.7 in the printed copy*

Load tidyverse package:

``` r
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
```

The **tibble** package provides opinionated data frames that make working in the tidyverse a little easier. Note there's a `tibble` package, and a `tibble()` function.

The class of a tibble is `tbl_df`.

#### Creating Tibbles

-   To coerce a dataframe into a tibble, use `as_tibble()`
-   To create a new tibble from individual vectors, use `tibble()`:
    -   Recycles inputs of length 1
    -   Never converts strings to factors
-   Tibbles can have **non-syntactic** variable names - to refer to them use backticks
-   `tribble()` creates a transposed tibble: column headings are defined by formulas (i.e. they start with ~), and entries are separated by commas. This makes it possible to lay out small amounts of data in easy to read form.

#### Tibbles vs. `data.frame`

1.  Printing

-   Tibbles show only the first 10 rows and all columns that fit in the view
-   Each column reports its type (like `str`)
-   Explicitly `print()` the data frame and control the number of rows (`n`) and the `width` of the display (`width = Inf` will display all columns)
-   Rstudio's built in data viewer: `View()`

1.  Subsetting

-   Tibbles don't to partial matching and they will generate a warning if the column you're trying to extract doesn't exist
-   To pull out a single variable, use `$` or `[[`:
    -   `[[` can extract by name or position
    -   `$` can only extract by name
-   To use the above in a pipe, you'll need to use a special placeholder `.`, for example:

``` r
# Sample tibble:
df <- tibble(
  x = runif(5),
  y = rnorm(5)
)

# Extract by name:
df %>% .$x
```

    ## [1] 0.88226933 0.08180882 0.58617740 0.86181842 0.55351910

``` r
# Extract by position:
df %>% .[["x"]]
```

    ## [1] 0.88226933 0.08180882 0.58617740 0.86181842 0.55351910

#### Interacting with older code

-   Use `as.data.frame()` to turn a tibble back to a `data.frame`
-   With base R data frames, `[` sometimes returns a data frame, and sometimes returns a vector. With tibbles, `[` always returns another tibble

### Practice

**1.) How can you tell if an object is a tibble? (Hint: try printing `mtcars`, which is a regular data frame)**

``` r
class(mtcars)
```

    ## [1] "data.frame"

``` r
as_tibble(mtcars) %>% class()
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

``` r
as_tibble(mtcars) %>% is.tibble()
```

    ## [1] TRUE

**Answer**: instead of printing the objects and comparing the output (\# rows and columns on screen), you can get the `class()` of the object. The class of `mtcars` is a `data.frame`, however when we convert it to a tibble using `as_tibble()` and then find the class, the output is now "tbl\_df" "tbl" "data.frame", or the tibble class. Another way to check if an object is a tibble is to use the `as.tibble()` function which prints `TRUE` if the object is a tibble.

**2.) Compare and contrast the following operations on a `data.frame` and equivalent tibble. What is different? Why might the default data frame behaviours cause you frustration?**

``` r
# as a dataframe:
df <- data.frame(abc = 1, xyz = "a")
df$x # shows values and levels for column xyz
df[, "xyz"] # shows values and levels for column xyz
df[, c("abc", "xyz")] # shows values for columns abc and xyz

# as a tibble:
tib <- as_tibble(df)
tib$x # error: Unknown or uninitialized column: 'x'.NULL
tib[, "xyz"] # shows xyz as a factor with one row and the value a
tib[, c("abc", "xyz")] # shows abc and xyz as a factor with 1 row and values 1 and a
```

**Answer**: `$` on a dataframe allows for partial matching, so `df$x` is interpreted as `df$xyz`. This saves a few keystrokes, but can result in accidentally using a different variable than you thought you were using. Base R also coerces characters into factors, which can be frustrating, and tibbles do not. When it comes to `[[`, a data frame only returns columns that are explicitly referenced and a tibble returns the entire observation (row).

**3.) If you have the name of a variable stored in an object, e.g. `var <- "mpg"`, how can you extract the reference variable from a tibble?**

``` r
#var <- "mpg"
#class(var)
#var$hwy 
#var["hwy"] # prints first 10 rows and class of hwy 
#var[["hwy"]] # prints all 234 values
```

**Answer**: I'm not sure I understand what this question is asking. According to the [documentation](https://cran.r-project.org/web/packages/tibble/tibble.pdf), one bracket `[`, does not simplify (drop) by default and returns a data frame. Two brackets `[[` calls the subset directly, so is considerably faster and returns `NULL` if column does not exist. You can't use the `$` in this example because it looks for a column name.\*\*

**4.) Practice referring to non-syntactic names in the following data frame by:**

**- 4.a.) Extracting the variable called `1`.**

**- 4.b.) Plotting a scatterplot of `1` vs `2`.**

**- 4.c.) Creating a new column called `3` which is `2` divided by `1`.**

**- 4.d.) Renaming the columns to `one`, `two`, and `three`.**

``` r
annoying <- tibble(
  `1` = 1:10,
  `2` = `1` * 2 + rnorm(length(`1`))
)
```

**Answer**:

``` r
# 4.a.) Extracting the variable called 1:
annoying$`1`
```

    ##  [1]  1  2  3  4  5  6  7  8  9 10

``` r
# 4.b.) Plotting a scatterplot of 1 vs 2:
ggplot(annoying, aes(`1`, `2`)) +
    geom_point()
```

![](tibbles_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
# 4.c.) Creating a new column called `3` which is `2` divided by `1`:
annoying %>%
    mutate(`3` = `2` / `1`)
```

    ## Warning: package 'bindrcpp' was built under R version 3.4.4

    ## # A tibble: 10 x 3
    ##      `1`    `2`   `3`
    ##    <int>  <dbl> <dbl>
    ##  1     1  0.131 0.131
    ##  2     2  4.43  2.22 
    ##  3     3  5.69  1.90 
    ##  4     4  9.36  2.34 
    ##  5     5 10.2   2.03 
    ##  6     6 10.5   1.75 
    ##  7     7 13.3   1.90 
    ##  8     8 16.4   2.05 
    ##  9     9 17.4   1.93 
    ## 10    10 21.5   2.15

``` r
# 4.d.) Renaming the columns to `one`, `two`, and `three`:
annoying %>%
    mutate(`3` = `2` / `1`) %>%
    rename("one" = `1`,
           "two" = `2`,
           "three" = `3`)
```

    ## # A tibble: 10 x 3
    ##      one    two three
    ##    <int>  <dbl> <dbl>
    ##  1     1  0.131 0.131
    ##  2     2  4.43  2.22 
    ##  3     3  5.69  1.90 
    ##  4     4  9.36  2.34 
    ##  5     5 10.2   2.03 
    ##  6     6 10.5   1.75 
    ##  7     7 13.3   1.90 
    ##  8     8 16.4   2.05 
    ##  9     9 17.4   1.93 
    ## 10    10 21.5   2.15

**5.) What does `tibble::enframe()` do? When might you use it?**

**Answer**: `enframe()` converts named atomic vectors or lists to two-column data frames. For unnamed vectors, the natural sequence is used as name column. `deframe()` converts two-column data frames to a named vector or list, using the first column as name and the second column as vector. This might be useful if you have a named vector you can to build into a tibble. Lots of functions will return results in a list or names vector format when there are several values to be outputted.

**6.) What option controls how many additional column names are printed at the footer of a tibble?**

``` r
?print.tbl_df
```

**Answer**: the `n_extra` argument to `print.tbl_df()` controls the number of extra columns to print abbreviated information for, if the width is too small for the entire tibble. If `NULL`, the default, it will print information about at most `tibble.max_extra_cols` extra columns.

*That's all folks!*
