Functional Programming
================
Byron Tang
July 7, 2018

Anonymous functions
-------------------

#### 1. Given a function, like "mean", match.fun() lets you find a function. Given a function, can you find its name? Why doesn't that make sense in R?

If the function is anonymous, we could not find its name in any way. However, if the function is not anonymous, we'll need to give a function by its 'name' in order to find a name, which seems odd.

#### 2. Use lapply() and an anonymous function to find the coefficient of variation (the standard deviation divided by the mean) for all columns in the mtcars dataset.

``` r
lapply(mtcars, function(x) sd(x)/mean(x))
```

    ## $mpg
    ## [1] 0.2999881
    ## 
    ## $cyl
    ## [1] 0.2886338
    ## 
    ## $disp
    ## [1] 0.5371779
    ## 
    ## $hp
    ## [1] 0.4674077
    ## 
    ## $drat
    ## [1] 0.1486638
    ## 
    ## $wt
    ## [1] 0.3041285
    ## 
    ## $qsec
    ## [1] 0.1001159
    ## 
    ## $vs
    ## [1] 1.152037
    ## 
    ## $am
    ## [1] 1.228285
    ## 
    ## $gear
    ## [1] 0.2000825
    ## 
    ## $carb
    ## [1] 0.5742933

#### 3. Use integrate() and an anonymous function to find the area under the curve for the following functions. Use Wolfram Alpha to check your answers.

``` r
y = x ^ 2 - x, x in [0, 10]
y = sin(x) + cos(x), x in [-??, ??]
y = exp(x) / x, x in [10, 20]
```

Use integrate()

``` r
integrate(function(x) x ^ 2 - x, 0, 10)
```

    ## 283.3333 with absolute error < 3.1e-12

``` r
integrate(function(x) sin(x) + cos(x), -pi, pi)
```

    ## 2.615901e-16 with absolute error < 6.3e-14

``` r
integrate(function(x) exp(x) / x, 10, 20)
```

    ## 25613160 with absolute error < 2.8e-07

The answer is double checked with Wolfram Alpha. However, Wolfram Alpha calculates area under x axis as positive, while integrate() count it as negative. Here I keep the calculation of integrate as other materials also suggest the area under x asix should be negative.

Reference:

-   <https://amsi.org.au/ESA_Senior_Years/SeniorTopic3/3f/3f_2content_8.html>

Closures
--------

#### 1. Why are functions created by other functions called closures?

Closures get their name because they 'enclose' the environment of the parent function and can access all its variables.

#### 2. What does the following statistical function do? What would be a better name for it? (The existing name is a bit of a hint.)

``` r
bc <- function(lambda) {
  if (lambda == 0) {
    function(x) log(x)
  } else {
    function(x) (x ^ lambda - 1) / lambda
  }
}
```

The function is Box-Cox transformation: <https://en.wikipedia.org/wiki/Power_transform#Box%E2%80%93Cox_transformation>

A name that is more informatinve, like BCTransform, would be better.

#### 3. What does approxfun() do? What does it return?

The function approxfun() returns a function performing (linear or constant) interpolation of the given data points. For a given set of x values, this function will return the corresponding interpolated values. It uses data stored in its environment when it was created, the details of which are subject to change.

#### 4. What does ecdf() do? What does it return?

Compute an empirical cumulative distribution function, with several methods for plotting, printing and computing with such an 'ecdf' object. The function ecdf() returns an ecdf project, which is also a function.

``` r
x <- rnorm(12)
Fn <- ecdf(x)
Fn     # a *function*
```

    ## Empirical CDF 
    ## Call: ecdf(x)
    ##  x[1:12] = -0.99592, -0.83648, -0.73232,  ..., 0.84061, 0.99741

``` r
# Class of Fn
class(Fn)
```

    ## [1] "ecdf"     "stepfun"  "function"

#### 5. Create a function that creates functions that compute the ith central moment of a numeric vector. You can test it by running the following code:

``` r
m1 <- moment(1)
m2 <- moment(2)

x <- runif(100)
stopifnot(all.equal(m1(x), 0))
stopifnot(all.equal(m2(x), var(x) * 99 / 100))
```

Create function

``` r
moment <- function(m){
  function(x){
    mean((x - mean(x)) ^ m)
  }
}
```

Test function: Codes run without error.

``` r
m1 <- moment(1)
m2 <- moment(2)

x <- runif(100)
stopifnot(all.equal(m1(x), 0))
stopifnot(all.equal(m2(x), var(x) * 99 / 100))
```

#### 6. Create a function pick() that takes an index, i, as an argument and returns a function with an argument x that subsets x with i.

``` r
lapply(mtcars, pick(5))
# should do the same as this
lapply(mtcars, function(x) x[[5]])
```

Create function

``` r
pick <- function(i){
  function(x){
    len <- length(x)
    stopifnot(i <= len)
    x[[i]]
  }
}
```

Test function

``` r
identical(lapply(mtcars, pick(5)), lapply(mtcars, function(x) x[[5]]))
```

    ## [1] TRUE

List of functions
-----------------

#### 1. Implement a summary function that works like base::summary(), but uses a list of functions. Modify the function so it returns a closure, making it possible to use it as a function factory.

Create funciton (for numeric inputs only)

``` r
summary2 <- function(v){

  functions <- list(
    Min = function(x) min(x),
    Qu_1st = function(x) quantile(x, .25),
    Median = function(x) median(x),
    Mean = function(x) mean(x),
    Qu_3rd = function(x) quantile(x, .75),
    Max = function(x) max(x)
    )
 
  if (!is.data.frame(v)){
    df <- data.frame(lapply(functions,
                            function(f) format(round(f(v), 2), nsmall = 2)),
                     stringsAsFactors = FALSE)
    row.names(df) <- deparse(substitute(v))
  } else if(is.data.frame(v)){
    df <- data.frame(lapply(functions,
                            function(f) format(lapply(mtcars, f))),
                     stringsAsFactors = FALSE)
  }
  t(round(data.matrix(df, rownames.force = NA), 2))
}
```

Test function

``` r
summary2(mtcars$mpg)
```

    ##        mtcars$mpg
    ## Min         10.40
    ## Qu_1st      15.43
    ## Median      19.20
    ## Mean        20.09
    ## Qu_3rd      22.80
    ## Max         33.90

``` r
summary2(mtcars)
```

    ##          mpg  cyl   disp     hp drat   wt  qsec   vs   am gear carb
    ## Min    10.40 4.00  71.10  52.00 2.76 1.51 14.50 0.00 0.00 3.00 1.00
    ## Qu_1st 15.43 4.00 120.83  96.50 3.08 2.58 16.89 0.00 0.00 3.00 2.00
    ## Median 19.20 6.00 196.30 123.00 3.69 3.33 17.71 0.00 0.00 4.00 2.00
    ## Mean   20.09 6.19 230.72 146.69 3.60 3.22 17.85 0.44 0.41 3.69 2.81
    ## Qu_3rd 22.80 8.00 326.00 180.00 3.92 3.61 18.90 1.00 1.00 4.00 4.00
    ## Max    33.90 8.00 472.00 335.00 4.93 5.42 22.90 1.00 1.00 5.00 8.00

Function Factory

Note: The behavior of the new function is different from summary2() when taking in a data frame as input.

``` r
summary_fac <- function(f){

  functions <- list(
    Min = function(x) min(x),
    Qu_1st = function(x) quantile(x, .25),
    Median = function(x) median(x),
    Mean = function(x) mean(data.matrix(x)),
    Qu_3rd = function(x) quantile(x, .75),
    Max = function(x) max(x)
    )
  f <- functions[[f]]
}
```

Test function

``` r
fun_names <- c("Min", "Max", "Mean")
funcs <- lapply(setNames(fun_names, fun_names), summary_fac)
with(funcs, paste0("Min: ", Min(mtcars$mpg), 
                   "; Max: ", Max(mtcars$mpg), 
                   "; Mean: ", Mean(mtcars$mpg)))
```

    ## [1] "Min: 10.4; Max: 33.9; Mean: 20.090625"

``` r
with(funcs, paste0("Min: ", Min(mtcars), 
                   "; Max: ", Max(mtcars), 
                   "; Mean: ", Mean(mtcars)))
```

    ## [1] "Min: 0; Max: 472; Mean: 39.6085284090909"

#### 2. Which of the following commands is equivalent to with(x, f(z))?

``` r
x$f(x$z).
f(x$z).
x$f(z).
f(z).
It depends.
```

x$f(z)

Case study: numerical integration
---------------------------------

#### 1. Instead of creating individual functions (e.g., midpoint(), trapezoid(), simpson(), etc.), we could store them in a list. If we did that, how would that change the code? Can you create the list of functions from a list of coefficients for the Newton-Cotes formulae?

Create function

``` r
integrationFuns <- function(f, a, b, n = 10, rule = "midpoint", open = FALSE){
  
  # Create a closure of Newton-cotes
  newton_cotes <- function(coef, open = FALSE) {
    n <- length(coef) + open
    function(f, a, b) {
      pos <- function(i) a + i * (b - a) / n
      points <- pos(seq.int(0, length(coef) - 1))
        
      (b - a) / sum(coef) * sum(f(points) * coef)
    }
  }
  
  # Create a list of functions
  functions <- list(
    midpoint <- function(f, a, b) (b - a) * f((a + b) / 2),
    trapezoid <- function(f, a, b) (b - a) / 2 * (f(a) + f(b)),
    simpson <- function(f, a, b) (b - a) / 6 * (f(a) + 4 * f((a + b) / 2) + f(b)),
    boole <- newton_cotes(coef = c(7, 32, 12, 32, 7), open = open),
    milne <- newton_cotes(coef = c(2, -1, 2), open = open)
  )
  names(functions) <- c("midpoint", "trapezoid", "simpson", "boole", "milne")
  
  # Calculate composite integration
  points <- seq(a, b, length = n + 1)
  area <- 0
  for (i in seq_len(n)) {
    area <- area + functions[[rule]](f, points[i], points[i + 1])
  }
  area
}
```

Test function

``` r
message("Default to midpoint: ", integrationFuns(sin, 0, pi))
```

    ## Default to midpoint: 2.00824840790797

``` r
sapply(c("trapezoid", "simpson", "boole"), 
       function(x) integrationFuns(sin, 0, pi, rule = x))
```

    ## trapezoid   simpson     boole 
    ##  1.983524  2.000007  2.001979

``` r
message("milne: ", 
        integrationFuns(sin, 0, pi, rule = "milne", open = TRUE))
```

    ## milne: 1.99382874751648

Reference: <https://en.wikipedia.org/wiki/Newton%E2%80%93Cotes_formulas>

#### 2. The trade-off between integration rules is that more complex rules are slower to compute, but need fewer pieces. For sin() in the range \[0, ??\], determine the number of pieces needed so that each rule will be equally accurate. Illustrate your results with a graph. How do they change for different functions? sin(1 / x^2) is particularly challenging.

Create function

``` r
integrationFuns2 <- function(f, a, b, n = 100, accu = 5, increment = 1) {
  check = FALSE
  
  # Use the output of integrate as the benchmark of accuracy
  trueValue <- integrate(f = f, lower = a, upper = b)
  
  while (check == FALSE){
    allValue <- sapply(c("midpoint", "trapezoid", "simpson", "boole", "milne"),
       function(x) integrationFuns(f = f, a = a, b = b, n = n, rule = x))
    
    # Caculate accuracy to the nth decimal point (default to 5)
    compValue <- round(allValue - trueValue$value, accu)
    
    check <- all(compValue[1] == compValue)
    n = n + increment
  }

  list("TrueValue" = trueValue, 
       "FinalValues" = format(round(allValue, accu), nsmall = accu), 
       "Accuracy at decimal point" = accu, 
       "Accurate" = check, 
       "Pieces" = n)

}
```

Test function

``` r
integrationFuns2(sin, 0, pi)
```

    ## $TrueValue
    ## 2 with absolute error < 2.2e-14
    ## 
    ## $FinalValues
    ##  midpoint trapezoid   simpson     boole     milne 
    ## "2.00000" "2.00000" "2.00000" "2.00000" "2.00000" 
    ## 
    ## $`Accuracy at decimal point`
    ## [1] 5
    ## 
    ## $Accurate
    ## [1] TRUE
    ## 
    ## $Pieces
    ## [1] 575

Test sin(1 / x^2)

``` r
testF <- function(x) sin(1/x^2)
integrationFuns2(testF, 1, pi, n = 52500, increment = 10)
```

    ## $TrueValue
    ## 0.6493765 with absolute error < 1.2e-11
    ## 
    ## $FinalValues
    ##  midpoint trapezoid   simpson     boole     milne 
    ## "0.64938" "0.64938" "0.64938" "0.64938" "0.64938" 
    ## 
    ## $`Accuracy at decimal point`
    ## [1] 5
    ## 
    ## $Accurate
    ## [1] TRUE
    ## 
    ## $Pieces
    ## [1] 52860

Illustration by a graph is skipped at this moment.
