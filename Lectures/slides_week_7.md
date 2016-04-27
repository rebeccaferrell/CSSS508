CSSS 508, Week 7
===
author: Rebecca Ferrell
date: May 11, 2016
transition: rotate
width: 1100
height: 750



Writing functions
===
type: section


Examples of existing functions
===
incremental: true

* `mean`:
    + Input: a vector
    + Output: a single number
* `dplyr::filter`:
    + Input: a data frame, logical conditions
    + Output: a data frame with rows removed using those conditions
* `c`:
    + Input: a bunch of numbers or characters
    + Output: the inputs combined into a vector
* `readr::read_csv`:
    + Input: file path, optional things like variables names and types
    + Output: data frame containing info converted from file


Simple example of homebrewed function
===
incremental: true

Let's write a function that takes a vector as input and outputs a named vector of the first and last elements:


```r
first_and_last <- function(x) {
    first <- x[1]
    last <- x[length(x)]
    return(c("first" = first, "last" = last))
}
```

Test it out:


```r
first_and_last(c(4, 3, 1, 8))
```

```
first  last 
    4     8 
```


More testing of simple function
===
incremental: true

What if I give `first_and_last` a vector of length 1?


```r
first_and_last(7)
```

```
first  last 
    7     7 
```

Of length 0?


```r
first_and_last(numeric(0))
```

```
first 
   NA 
```

Maybe we want it to be a little smarter.


Checking inputs
===
incremental: true

Let's make sure we get an error message is the vector is too small:


```r
smarter_first_and_last <- function(x) {
    if(length(x) == 0L) {
        stop("The input has no length!")
    } else {
        first <- x[1]
        last <- x[length(x)]
        return(c("first" = first, "last" = last))        
    }
}
```

Testing the smarter function
===
incremental: true


```r
smarter_first_and_last(numeric(0))
```

```
Error in smarter_first_and_last(numeric(0)): The input has no length!
```

```r
smarter_first_and_last(c(4, 3, 1, 8))
```

```
first  last 
    4     8 
```

Cracking open functions
===
incremental: true

If you type the function name without any parentheses or arguments, you can see its guts:


```r
smarter_first_and_last
```

```
function(x) {
    if(length(x) == 0L) {
        stop("The input has no length!")
    } else {
        first <- x[1]
        last <- x[length(x)]
        return(c("first" = first, "last" = last))        
    }
}
```


Anatomy of a function
===
incremental: true

* Name: what you assign the function to so you can use it later
    + Can have anonymous functions (later today)
* Arguments (inputs, parameters): things the user passes to the function that affects how it works
    + e.g. `x` or `na.rm` in `my_new_func <- function(x, na.rm = FALSE) {...}`
    + `na.rm = FALSE` is example of setting a default value: if the user doesn't say what `na.rm` is, it'll be `FALSE`
* Body: the guts! Input type checking, conditionals, calculations, etc.
* Return value: the thing inside `return()` the function outputs. Could be a vector, list, data frame, another function, or even nothing
    + If unspecified, will be the last thing calculated (maybe not what you want?)
    

Comments on conditionals
===

For logical tests on whole vectors, we've been using `&` and `|` (e.g. with `dplyr::filter`). These are *vectorized* operators suitable for data.

When using `if` for conditional control flow in functions and loops, we only want to be testing **one** value. Best practice is to use `&&` and `||` instead of `&` and `|`:

* Only looks at first element of vector and ignores rest altogether
* Check from left to right and gives answer as soon as it knows for sure (e.g. seeing `FALSE` in first condition as part of `&&`, will evaluate to `FALSE` right away without checking rest of conditions)


summarize_each, mutate_each
===
type: section


summarize_each
===

I mentioned `summarize_each` and `mutate_each` back in the `dplyr` lecture. These take an argument `funs()`, and your own functions can go in there just like normal ones:


```r
library(dplyr)
mean_abs_dev <- function(x, na.rm = FALSE) {
    abs_dev <- abs(x - mean(x, na.rm = na.rm))
    return(mean(abs_dev, na.rm = na.rm))
}
swiss %>%
    summarize_each(funs(mean, sd, mean_abs_dev),
                   Catholic, Fertility)
```

```
  Catholic_mean Fertility_mean Catholic_sd Fertility_sd
1      41.14383       70.14255    41.70485      12.4917
  Catholic_mean_abs_dev Fertility_mean_abs_dev
1              38.62839               9.577818
```

Anonymous functions in dplyr
===

You can skip even naming your function if you won't use it again. Code below will return the mean divided by the standard deviation for each variable in `swiss`:


```r
swiss %>%
    summarize_each(funs(mean(., na.rm = TRUE) / sd(., na.rm = TRUE)))
```

```
  Fertility Agriculture Examination Education  Catholic Infant.Mortality
1  5.615134    2.230597    2.066884  1.141785 0.9865478         6.846766
```


Example passing function with arguments
===

From the data downloading demo you read before today, I had this block of code:


```r
CA_OSHPD_util <- CA_OSHPD_util %>%
    # translating the regular expression pattern:
    # \\. matches the location of the period.
    # 0+ matches at least one zero and possibly more following that period.
    # replacement for period + 0s is nothing (empty string)
    mutate_each(funs(gsub(pattern = "\\.0+",
                          x = .,
                          replacement = "")),
                # variables to fix
                FAC_NO, HSA, HFPA)
```


Standard and non-standard evaluation
===

`dplyr` uses what is called **non-standard evaluation** that lets you refer to "naked" variables without putting quotes around them like `FAC_NO, HSA, HFPA`. There are **standard evaluation** versions of `dplyr` functions that use the quoted versions instead, which can sometimes be more convenient. These end in an underscore (`_`).

Example converting character data to dates from the data downloading demo:

```r
yearly_frames[[i]] <- yearly_frames[[i]] %>%
    # cnames is a character vector of var names
    # 4th and 5th variables are strings to become dates
    mutate_each_(funs(mdy), cnames[4:5])
```


lapply
===
type: section


lapply: list + applying functions
===


Anonymous functions in base R
===

Like with `dplyr`, you can use anonymous functions, but the difference is you'll need to have the `function` part at the beginning:


```r
lapply(swiss, function(x) mean(x, na.rm = TRUE) / sd(x, na.rm = TRUE))
```

```
$Fertility
[1] 5.615134

$Agriculture
[1] 2.230597

$Examination
[1] 2.066884

$Education
[1] 1.141785

$Catholic
[1] 0.9865478

$Infant.Mortality
[1] 6.846766
```

replicate
===

Use `replicate` with repeated random number simulations

example: illustrate central limit theorem


apply
===

apply is trash but old-school R programmers use it and functional programming people like it, so you'll see it and you need to know what it is, but it's not faster than a for loop and debatably clearer



Homework
===

Bootstrapping simulation:

- First, randomly generate data, then fit a model, and then do this repeatedly. The standard error is the standard deviation over the estimates of the quantity. This gives us the "truth" for what the standard error is.

- Next, we bootstrap. Randomly generate one set of data. You'll do the sample thing as above, but now you can't generate more new data, so you'll instead sample from it with replacement, then fit the model repeatedly.

- As a statistical alternative, you will just fit the model once on the above data, and use the standard SE estimates

- Use ggplot's qqplot to compare the true sampling distribution of the estimator with the bootstrapped estimate and the estimate you get assuming normality and standard error = SD.
