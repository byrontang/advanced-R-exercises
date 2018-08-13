Exceptions and Debugging
================
Byron Tang
July 5, 2018

Condition Handling
------------------

#### 1. Compare the following two implementations of message2error(). What is the main advantage of withCallingHandlers() in this scenario? (Hint: look carefully at the traceback.)

``` r
message2error <- function(code) {
  withCallingHandlers(code, message = function(e) stop(e))
}
message2error <- function(code) {
  tryCatch(code, message = function(e) stop(e))
}
```

The handlers in withCallingHandlers() are called in the context of the call that generated the condition whereas the handlers in tryCatch() are called in the context of tryCatch().

A main advantage of withCallingHandlers() is that the execution continues normally when the handler returns. With tryCatch(), the flow of execution is interrupted when a handler is called.

Defensive programming
---------------------

#### 1. The goal of the col\_means() function defined below is to compute the means of all numeric columns in a data frame.

``` r
col_means <- function(df) {
  numeric <- sapply(df, is.numeric)
  numeric_cols <- df[, numeric]

  data.frame(lapply(numeric_cols, mean))
}
```

#### However, the function is not robust to unusual inputs. Look at the following results, decide which ones are incorrect, and modify col\_means() to be more robust. (Hint: there are two function calls in col\_means() that are particularly prone to problems.)

``` r
col_means(mtcars)
col_means(mtcars[, 0])
col_means(mtcars[0, ])
col_means(mtcars[, "mpg", drop = F])
col_means(1:10)
col_means(as.matrix(mtcars))
col_means(as.list(mtcars))

mtcars2 <- mtcars
mtcars2[-1] <- lapply(mtcars2[-1], as.character)
col_means(mtcars2)
```

Revise the function

``` r
col_means_re <- function(df) {
  # Stop if the input is not a data frame
  stopifnot(is.data.frame(df))
  
  # Use vapply() rather than sapply so that a vector or array will always return
  # (sapply() returns an unmaed list when output length = 0)
  numeric <- vapply(df, is.numeric, logical(1)) 
  
  # Use drop = FALSE to avoid accidentally converting 
  # 1-column data frames into vectors.
  numeric_cols <- df[, numeric, drop = FALSE]

  data.frame(lapply(numeric_cols, mean))
}
```

Note: Why is vapply safer then sapply

-   <https://stackoverflow.com/questions/12339650/why-is-vapply-safer-than-sapply>

With the revised function, all the cases return expected results.

Test revised function

``` r
try(col_means_re(mtcars))
```

    ##        mpg    cyl     disp       hp     drat      wt     qsec     vs
    ## 1 20.09062 6.1875 230.7219 146.6875 3.596563 3.21725 17.84875 0.4375
    ##        am   gear   carb
    ## 1 0.40625 3.6875 2.8125

``` r
try(col_means_re(mtcars[, 0]))
```

    ## data frame with 0 columns and 0 rows

``` r
try(col_means_re(mtcars[0, ]))
```

    ##   mpg cyl disp  hp drat  wt qsec  vs  am gear carb
    ## 1 NaN NaN  NaN NaN  NaN NaN  NaN NaN NaN  NaN  NaN

``` r
try(col_means_re(mtcars[, "mpg", drop = F]))
```

    ##        mpg
    ## 1 20.09062

``` r
cat(try(col_means_re(1:10)))
```

    ## Error in col_means_re(1:10) : is.data.frame(df) is not TRUE

``` r
cat(try(col_means_re(as.matrix(mtcars))))
```

    ## Error in col_means_re(as.matrix(mtcars)) : is.data.frame(df) is not TRUE

``` r
cat(try(col_means_re(as.list(mtcars))))
```

    ## Error in col_means_re(as.list(mtcars)) : is.data.frame(df) is not TRUE

``` r
mtcars2 <- mtcars
mtcars2[-1] <- lapply(mtcars2[-1], as.character)
try(col_means_re(mtcars2))
```

    ##        mpg
    ## 1 20.09062

#### 2. The following function "lags" a vector, returning a version of x that is n values behind the original. Improve the function so that it (1) returns a useful error message if n is not a vector, and (2) has reasonable behaviour when n is 0 or longer than x.

``` r
lag <- function(x, n = 1L) {
  xlen <- length(x)
  c(rep(NA, n), x[seq_len(xlen - n)])
}
```

Revise the function

``` r
lag <- function(x, n = 1L) {
  # (1) returns a useful error message if n is not a vector
  if (!is.vector(x))
    stop(("Error: input is not a vector"))
  
  # (2) has reasonable behaviour when n is 0 or longer than x
  stopifnot(n != 0)
  stopifnot(n <= length(x))
  
  xlen <- length(x)
  c(rep(NA, n), x[seq_len(xlen - n)])
}
```

Test the function

``` r
lag(c(1:5))
```

    ## [1] NA  1  2  3  4

``` r
cat(try(lag(mtcars)))
```

    ## Error in lag(mtcars) : Error: input is not a vector

``` r
cat(try(lag(c(1:5), 0)))
```

    ## Error in lag(c(1:5), 0) : n != 0 is not TRUE

``` r
cat(try(lag(c(1:5), 6)))
```

    ## Error in lag(c(1:5), 6) : n <= length(x) is not TRUE
