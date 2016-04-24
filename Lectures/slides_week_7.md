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


Examples of R functions
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






```
Error in smarter_first_and_last(numeric(0)) : The input has no length!
```
