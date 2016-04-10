CSSS 508, Week 6
===
author: Rebecca Ferrell
date: May 4, 2016
transition: rotate
width: 1100
height: 750


Why we want to do loopy-things
===
incremental: true

Avoid repetition ("**D**on't **R**epeat **Y**ourself") and prevent inconsistencies:

* Import many similar data files, clean up each, glue them together
* Partition data, fit same model to each part separately
* For each item in a list, do the same thing to it
* Study behavior of models under many different samples of data

Things to identify:

1. What we're looping over
2. What we do in the loop
3. Is what happens in the loop affected by previous iterations?


for loops
===
type: section


for loops
===
incremental: true

`for` loops are the most basic and general kind of loop. You give it a vector of indices, it assigns to first value to some variable, does stuff, increments the variable to the next value, and keeps going doing more stuff until it runs out of indices.


```r
for(i in 1:3) {
    # inside for, output won't show up w/o "print"
    print(i^2) 
}
```

```
[1] 1
[1] 4
[1] 9
```

Iterating over indices `1:n` is very common. `n` might be number of rows/columns in matrix or data frame, or length of vector calculated using `nrow`, `ncol`, `length`.


Iterate over character vectors
===
incremental: true

You can also iterate over a character vector:

```r
some_letters <- letters[4:7]
for(i in some_letters) {
    print(i)
}
```

```
[1] "d"
[1] "e"
[1] "f"
[1] "g"
```

```r
i # the index variable was added to environment
```

```
[1] "g"
```


seq_along
===
incremental: true

When you want to iterate over something that isn't numeric but want to keep numeric track of where you are in the loop, `seq_along` is useful:


```r
for(a in seq_along(some_letters)) {
    print(paste0("Letter ", a, ": ", some_letters[a]))
}
```

```
[1] "Letter 1: d"
[1] "Letter 2: e"
[1] "Letter 3: f"
[1] "Letter 4: g"
```

```r
a
```

```
[1] 4
```


Pre-allocation
===
incremental: true

Usually in a `for` loop, you aren't printing output, but want to store results from each iteration somewhere. Figure out how you want to store the output (vector, matrix, data frame, list, etc.) and then **pre-allocate** an object for that (typically with missing values as placeholders).


```r
# preallocate vector of NAs
iters <- 5
output <- rep(NA, iters)

for(i in 1:iters) {
    output[i] <- (i-1)^2 + (i-2)^2
}
output
```

```
[1]  1  1  5 13 25
```


Preallocated list: regression models
===
incremental: true


```r
x <- rnorm(30) # making fake data
fake_data <- data.frame(x = x, y = 2 * x + rnorm(30))
# model formulas as strings in named vector
models <- c("int only" = "y ~ 1", "std" = "y ~ x")
output <- vector("list", length(models)) # empty list
names(output) <- names(models)
# fit each model to fake_data and store in output
for(mod in names(models)) {
    output[[mod]] <- lm(formula(models[mod]),
                        data = fake_data)
}
str(output)
```

```
List of 2
 $ int only:List of 11
  ..$ coefficients : Named num 0.816
  .. ..- attr(*, "names")= chr "(Intercept)"
  ..$ residuals    : Named num [1:30] 2.81 -4.88 -2.68 2.02 3.53 ...
  .. ..- attr(*, "names")= chr [1:30] "1" "2" "3" "4" ...
  ..$ effects      : Named num [1:30] -4.47 -5.31 -3.11 1.58 3.1 ...
  .. ..- attr(*, "names")= chr [1:30] "(Intercept)" "" "" "" ...
  ..$ rank         : int 1
  ..$ fitted.values: Named num [1:30] 0.816 0.816 0.816 0.816 0.816 ...
  .. ..- attr(*, "names")= chr [1:30] "1" "2" "3" "4" ...
  ..$ assign       : int 0
  ..$ qr           :List of 5
  .. ..$ qr   : num [1:30, 1] -5.477 0.183 0.183 0.183 0.183 ...
  .. .. ..- attr(*, "dimnames")=List of 2
  .. .. .. ..$ : chr [1:30] "1" "2" "3" "4" ...
  .. .. .. ..$ : chr "(Intercept)"
  .. .. ..- attr(*, "assign")= int 0
  .. ..$ qraux: num 1.18
  .. ..$ pivot: int 1
  .. ..$ tol  : num 1e-07
  .. ..$ rank : int 1
  .. ..- attr(*, "class")= chr "qr"
  ..$ df.residual  : int 29
  ..$ call         : language lm(formula = formula(models[mod]), data = fake_data)
  ..$ terms        :Classes 'terms', 'formula' length 3 y ~ 1
  .. .. ..- attr(*, "variables")= language list(y)
  .. .. ..- attr(*, "factors")= int(0) 
  .. .. ..- attr(*, "term.labels")= chr(0) 
  .. .. ..- attr(*, "order")= int(0) 
  .. .. ..- attr(*, "intercept")= int 1
  .. .. ..- attr(*, "response")= int 1
  .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
  .. .. ..- attr(*, "predvars")= language list(y)
  .. .. ..- attr(*, "dataClasses")= Named chr "numeric"
  .. .. .. ..- attr(*, "names")= chr "y"
  ..$ model        :'data.frame':	30 obs. of  1 variable:
  .. ..$ y: num [1:30] 3.63 -4.06 -1.86 2.83 4.35 ...
  .. ..- attr(*, "terms")=Classes 'terms', 'formula' length 3 y ~ 1
  .. .. .. ..- attr(*, "variables")= language list(y)
  .. .. .. ..- attr(*, "factors")= int(0) 
  .. .. .. ..- attr(*, "term.labels")= chr(0) 
  .. .. .. ..- attr(*, "order")= int(0) 
  .. .. .. ..- attr(*, "intercept")= int 1
  .. .. .. ..- attr(*, "response")= int 1
  .. .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
  .. .. .. ..- attr(*, "predvars")= language list(y)
  .. .. .. ..- attr(*, "dataClasses")= Named chr "numeric"
  .. .. .. .. ..- attr(*, "names")= chr "y"
  ..- attr(*, "class")= chr "lm"
 $ std     :List of 12
  ..$ coefficients : Named num [1:2] 0.195 2.262
  .. ..- attr(*, "names")= chr [1:2] "(Intercept)" "x"
  ..$ residuals    : Named num [1:30] 0.861 -1.709 -0.338 0.224 -1.473 ...
  .. ..- attr(*, "names")= chr [1:30] "1" "2" "3" "4" ...
  ..$ effects      : Named num [1:30] -4.4701 12.877 -0.1735 -0.0767 -2.1364 ...
  .. ..- attr(*, "names")= chr [1:30] "(Intercept)" "x" "" "" ...
  ..$ rank         : int 2
  ..$ fitted.values: Named num [1:30] 2.77 -2.35 -1.53 2.61 5.82 ...
  .. ..- attr(*, "names")= chr [1:30] "1" "2" "3" "4" ...
  ..$ assign       : int [1:2] 0 1
  ..$ qr           :List of 5
  .. ..$ qr   : num [1:30, 1:2] -5.477 0.183 0.183 0.183 0.183 ...
  .. .. ..- attr(*, "dimnames")=List of 2
  .. .. .. ..$ : chr [1:30] "1" "2" "3" "4" ...
  .. .. .. ..$ : chr [1:2] "(Intercept)" "x"
  .. .. ..- attr(*, "assign")= int [1:2] 0 1
  .. ..$ qraux: num [1:2] 1.18 1.27
  .. ..$ pivot: int [1:2] 1 2
  .. ..$ tol  : num 1e-07
  .. ..$ rank : int 2
  .. ..- attr(*, "class")= chr "qr"
  ..$ df.residual  : int 28
  ..$ xlevels      : Named list()
  ..$ call         : language lm(formula = formula(models[mod]), data = fake_data)
  ..$ terms        :Classes 'terms', 'formula' length 3 y ~ x
  .. .. ..- attr(*, "variables")= language list(y, x)
  .. .. ..- attr(*, "factors")= int [1:2, 1] 0 1
  .. .. .. ..- attr(*, "dimnames")=List of 2
  .. .. .. .. ..$ : chr [1:2] "y" "x"
  .. .. .. .. ..$ : chr "x"
  .. .. ..- attr(*, "term.labels")= chr "x"
  .. .. ..- attr(*, "order")= int 1
  .. .. ..- attr(*, "intercept")= int 1
  .. .. ..- attr(*, "response")= int 1
  .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
  .. .. ..- attr(*, "predvars")= language list(y, x)
  .. .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
  .. .. .. ..- attr(*, "names")= chr [1:2] "y" "x"
  ..$ model        :'data.frame':	30 obs. of  2 variables:
  .. ..$ y: num [1:30] 3.63 -4.06 -1.86 2.83 4.35 ...
  .. ..$ x: num [1:30] 1.136 -1.126 -0.761 1.066 2.488 ...
  .. ..- attr(*, "terms")=Classes 'terms', 'formula' length 3 y ~ x
  .. .. .. ..- attr(*, "variables")= language list(y, x)
  .. .. .. ..- attr(*, "factors")= int [1:2, 1] 0 1
  .. .. .. .. ..- attr(*, "dimnames")=List of 2
  .. .. .. .. .. ..$ : chr [1:2] "y" "x"
  .. .. .. .. .. ..$ : chr "x"
  .. .. .. ..- attr(*, "term.labels")= chr "x"
  .. .. .. ..- attr(*, "order")= int 1
  .. .. .. ..- attr(*, "intercept")= int 1
  .. .. .. ..- attr(*, "response")= int 1
  .. .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
  .. .. .. ..- attr(*, "predvars")= language list(y, x)
  .. .. .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
  .. .. .. .. ..- attr(*, "names")= chr [1:2] "y" "x"
  ..- attr(*, "class")= chr "lm"
```


Conditional flow
===
type: section

if, else
===
type: incremental

You've seen the `ifelse()` function before, which did logical checks on a whole vector. For checking whether a single condition holds and doing more complex actions, you can use `if()` and `else`:


```r
for(i in 1:10) {
    if(i %% 2 == 0) {
        print(paste0("The number ", i, " is even"))
    } else if(i %% 3 == 0) {
        print(paste0("The number ", i, " is divisible by 3"))
    } else {
        print(paste0("The number ", i, " is not divisible by 2 or 3"))
    }
}
```

Warning! R usually doesn't care about line breaks, but `else` needs to be on same line as the closing brace `}` of previous `if()`.

if, else: simple example
===
incremental: true


```
[1] "The number 1 is not divisible by 2 or 3"
[1] "The number 2 is even"
[1] "The number 3 is not even but divisible by 3"
[1] "The number 4 is even"
[1] "The number 5 is not divisible by 2 or 3"
[1] "The number 6 is even"
[1] "The number 7 is not divisible by 2 or 3"
[1] "The number 8 is even"
[1] "The number 9 is not even but divisible by 3"
[1] "The number 10 is even"
```


Example: downloading multiple files
===


```r
download.file("...",temp, mode="wb")
unzip(temp, "gbr_Country_en_csv_v2.csv")
dd <- read.table("gbr_Country_en_csv_v2.csv", sep=",",skip=2, header=T)
```



making a vector of file names for data chunked, then an empty list, then populating with data read in using list names, then using bind_rows on the list to put it all together, and cleaning up everything (rm, dir.create, deleting folders with unlink)

if and else as conditional modifiers, e.g. say data before one year were structured one way and data after a different way


Lab: cross-validation
===

take some data, partition into folds, fit model, save predictions for left-out data, look at how those predictions do. then do for more models (maybe increasing polynomial terms using `poly`), so we'll have nested loops.

for more info: [Introduction to Statistical Learning](http://www-bcf.usc.edu/~gareth/ISL/), Chapter 5.


while loops
===
incremental: true

Another looping structure (less common) is the `while` loop. Rather than iterating over a predefined vector, the loop keeps going until some condition is no longer true.


```r
num_heads <- 0; num_flips <- 0
while(num_heads < 4) {
    coin_flip <- rbinom(n = 1, size = 1, prob = 0.5)
    if(coin_flip == 1) { num_heads <- num_heads + 1 }
    num_flips <- num_flips + 1
}
num_heads
```

```
[1] 4
```

```r
num_flips # follows negative binomial distribution
```

```
[1] 9
```



for loops aren't perfect
===

In R, you can often do something faster or more clearly using some kind of alternative to a `for` loop:

* Vectorization: you don't need to loop because you're clever! **fast**
* `dplyr::do()` and `broom`: you want to partition a data frame and do the same thing to each partition **fast/clear**
* `lapply`: you want to apply the same function over a list **fast**
    + `replicate`: special case of `lapply` when you just want to call the same function a bunch of times with no changing inputs **fast**
* `apply`: apply the same function over rows or columns of a matrix **clear** (debatably)


Vectorization
===

If a function is vectorized, do not use a for loop! Don't do x^2 in a for loop for each value of x. Show the timing function.



dplyr::do()
===

In the case when the loop you want to do involves taking different pieces of your data and doing something with it, `do()` in the `dplyr` package is a nice option, especially when combined with the `broom` package.

example fitting separate linear trends in gapminder data for each country


lapply
===


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

- First, randomly generate data, then fit a model, and then do this repeatedly. The standard error is the standard deviation in the estimate of the quantity. This gives us the "truth" for what the standard error is.

- Next, we bootstrap. Randomly generate one set of data. You'll do the sample thing as above, but now you can't generate more new data, so you'll instead sample from it with replacement, then fit the model repeatedly.

- Use ggplot's qqplot to compare the true sampling distribution of the estimator with the bootstrapped estimate.

HW week 7: take HW 6 and now make it functional (do_one()), using replicate.
