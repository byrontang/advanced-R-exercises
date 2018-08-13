Functionals
================
Byron Tang
July 16, 2018

My first functional: lapply()
-----------------------------

#### 1. Why are the following two invocations of lapply() equivalent?

``` r
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(100)

lapply(trims, function(trim) mean(x, trim = trim))
lapply(trims, mean, x = x)
```

In both invocations, the trim argument in mean is supplied by each element in trims while the x argument is constantly the x vector. Therefore, the outputs are the same.

#### 2. The function below scales a vector so it falls in the range \[0, 1\]. How would you apply it to every column of a data frame? How would you apply it to every numeric column in a data frame?

``` r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}
```

Run the codes

``` r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}
```

Apply the function to every column of a data frame

``` r
df_num <- data.frame(lapply(mtcars, scale01))
head(df_num)
```

    ##         mpg cyl      disp        hp      drat        wt      qsec vs am
    ## 1 0.4510638 0.5 0.2217511 0.2049470 0.5253456 0.2830478 0.2333333  0  1
    ## 2 0.4510638 0.5 0.2217511 0.2049470 0.5253456 0.3482485 0.3000000  0  1
    ## 3 0.5276596 0.0 0.0920429 0.1448763 0.5023041 0.2063411 0.4892857  1  1
    ## 4 0.4680851 0.5 0.4662010 0.2049470 0.1474654 0.4351828 0.5880952  1  0
    ## 5 0.3531915 1.0 0.7206286 0.4346290 0.1797235 0.4927129 0.3000000  0  0
    ## 6 0.3276596 0.5 0.3838863 0.1872792 0.0000000 0.4978266 0.6809524  1  0
    ##   gear      carb
    ## 1  0.5 0.4285714
    ## 2  0.5 0.4285714
    ## 3  0.5 0.0000000
    ## 4  0.0 0.0000000
    ## 5  0.0 0.1428571
    ## 6  0.0 0.0000000

Apply to every numeric column in a data frame

``` r
mtcars2 <- mtcars
mtcars2$vs <- factor(mtcars2$vs)
mtcars2$am <- factor(mtcars2$am)
df_num2 <- data.frame(lapply(mtcars2[sapply(mtcars2, is.numeric)], scale01))
head(df_num2)
```

    ##         mpg cyl      disp        hp      drat        wt      qsec gear
    ## 1 0.4510638 0.5 0.2217511 0.2049470 0.5253456 0.2830478 0.2333333  0.5
    ## 2 0.4510638 0.5 0.2217511 0.2049470 0.5253456 0.3482485 0.3000000  0.5
    ## 3 0.5276596 0.0 0.0920429 0.1448763 0.5023041 0.2063411 0.4892857  0.5
    ## 4 0.4680851 0.5 0.4662010 0.2049470 0.1474654 0.4351828 0.5880952  0.0
    ## 5 0.3531915 1.0 0.7206286 0.4346290 0.1797235 0.4927129 0.3000000  0.0
    ## 6 0.3276596 0.5 0.3838863 0.1872792 0.0000000 0.4978266 0.6809524  0.0
    ##        carb
    ## 1 0.4285714
    ## 2 0.4285714
    ## 3 0.0000000
    ## 4 0.0000000
    ## 5 0.1428571
    ## 6 0.0000000

#### 3. Use both for loops and lapply() to fit linear models to the mtcars using the formulas stored in this list:

``` r
formulas <- list(
  mpg ~ disp,
  mpg ~ I(1 / disp),
  mpg ~ disp + wt,
  mpg ~ I(1 / disp) + wt
)
```

Run the codes

``` r
formulas <- list(
  mpg ~ disp,
  mpg ~ I(1 / disp),
  mpg ~ disp + wt,
  mpg ~ I(1 / disp) + wt
)
```

for loop

``` r
list_lm <- vector("list", length(formulas))
for (i in 1:length(formulas)){
  list_lm[[i]] <- lm(formulas[[i]], data = mtcars)
}
lapply(list_lm, function(x) round(x$coefficients, 5))
```

    ## [[1]]
    ## (Intercept)        disp 
    ##    29.59985    -0.04122 
    ## 
    ## [[2]]
    ## (Intercept)   I(1/disp) 
    ##    10.75202  1557.67393 
    ## 
    ## [[3]]
    ## (Intercept)        disp          wt 
    ##    34.96055    -0.01772    -3.35083 
    ## 
    ## [[4]]
    ## (Intercept)   I(1/disp)          wt 
    ##    19.02421  1142.55997    -1.79765

lapply

``` r
list_lm2 <- lapply(formulas, function(x) lm(x, data = mtcars))
lapply(list_lm2, function(x) round(x$coefficients, 5))
```

    ## [[1]]
    ## (Intercept)        disp 
    ##    29.59985    -0.04122 
    ## 
    ## [[2]]
    ## (Intercept)   I(1/disp) 
    ##    10.75202  1557.67393 
    ## 
    ## [[3]]
    ## (Intercept)        disp          wt 
    ##    34.96055    -0.01772    -3.35083 
    ## 
    ## [[4]]
    ## (Intercept)   I(1/disp)          wt 
    ##    19.02421  1142.55997    -1.79765

#### 4. Fit the model mpg ~ disp to each of the bootstrap replicates of mtcars in the list below by using a for loop and lapply(). Can you do it without an anonymous function?

``` r
bootstraps <- lapply(1:10, function(i) {
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  mtcars[rows, ]
})
```

for loop

``` r
list_lm3 <- vector("list", 10)
for(i in 1:10){
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  list_lm3[[i]] <- lm(mpg ~ disp, mtcars[rows, ])
}
sapply(list_lm3, function(x) round(x$coefficients, 5))
```

    ##                 [,1]     [,2]     [,3]     [,4]     [,5]     [,6]     [,7]
    ## (Intercept) 27.96573 29.34998 29.53801 26.23053 29.87379 27.94500 28.51878
    ## disp        -0.03458 -0.03769 -0.04128 -0.03068 -0.04129 -0.03451 -0.03560
    ##                 [,8]     [,9]    [,10]
    ## (Intercept) 28.69462 28.96864 29.86374
    ## disp        -0.03763 -0.04165 -0.04148

Combine all steps into one lapply() function. However, it's unavoidable to use an anonymous function.

Note: Need to set simplify as FALSE to keep data frame format from replicate()

``` r
list_lm4 <- lapply(replicate(10, mtcars[sample(1:nrow(mtcars), rep = TRUE), ],
                             simplify = FALSE), 
                   function(x) lm(mpg ~ disp, data = x))
sapply(list_lm4, function(x) round(x$coefficients, 5))
```

    ##                 [,1]     [,2]     [,3]     [,4]     [,5]     [,6]     [,7]
    ## (Intercept) 29.48285 29.49264 27.21547 31.21840 29.75826 30.84000 30.48726
    ## disp        -0.04152 -0.04202 -0.03279 -0.05051 -0.04006 -0.04462 -0.04424
    ##                 [,8]     [,9]    [,10]
    ## (Intercept) 28.20926 32.17073 28.86385
    ## disp        -0.03736 -0.04769 -0.03870

#### 5. For each model in the previous two exercises, extract R2 using the function below.

``` r
rsq <- function(mod) summary(mod)$r.squared
```

Extract R2

``` r
rsq <- function(mod) summary(mod)$r.squared
sapply(list_lm, rsq)
```

    ## [1] 0.7183433 0.8596865 0.7809306 0.8838038

``` r
sapply(list_lm2, rsq)
```

    ## [1] 0.7183433 0.8596865 0.7809306 0.8838038

``` r
sapply(list_lm3, rsq)
```

    ##  [1] 0.6391799 0.6307274 0.7367568 0.6918010 0.6669969 0.6338306 0.7260885
    ##  [8] 0.7587076 0.8115654 0.7046415

``` r
sapply(list_lm4, rsq)
```

    ##  [1] 0.8113400 0.6551340 0.6872138 0.7202997 0.7762047 0.7566495 0.7542395
    ##  [8] 0.6912865 0.7917928 0.6690644

For loop funcitonals: friends of lapply()
-----------------------------------------

#### 1. Use vapply() to:

#### a. Compute the standard deviation of every column in a numeric data frame.

#### b. Compute the standard deviation of every numeric column in a mixed data frame. (Hint: you'll need to use vapply() twice.)

a

``` r
vapply(mtcars, sd, numeric(1))
```

    ##         mpg         cyl        disp          hp        drat          wt 
    ##   6.0269481   1.7859216 123.9386938  68.5628685   0.5346787   0.9784574 
    ##        qsec          vs          am        gear        carb 
    ##   1.7869432   0.5040161   0.4989909   0.7378041   1.6152000

b

``` r
vapply(mtcars[, vapply(mtcars2, is.numeric, logical(1))], sd, numeric(1))
```

    ##         mpg         cyl        disp          hp        drat          wt 
    ##   6.0269481   1.7859216 123.9386938  68.5628685   0.5346787   0.9784574 
    ##        qsec        gear        carb 
    ##   1.7869432   0.7378041   1.6152000

#### 2. Why is using sapply() to get the class() of each element in a data frame dangerous?

The elements in a data frame might not have only one class. When any one of the element has more than one class, sapply would return a list instead of a vector. It might error out the following process or function without clear message if the expected output is a vector.

#### 3. The following code simulates the performance of a t-test for non-normal data. Use sapply() and an anonymous function to extract the p-value from every trial.

``` r
trials <- replicate(
  100, 
  t.test(rpois(10, 10), rpois(7, 10)),
  simplify = FALSE
)
```

#### Extra challenge: get rid of the anonymous function by using \[\[ directly.

Run the codes

``` r
trials <- replicate(
  100, 
  t.test(rpois(10, 10), rpois(7, 10)),
  simplify = FALSE
)
```

``` r
# use sapply() & an anonymous funciton
head(sapply(trials, function(x) x$p.value), 10)
```

    ##  [1] 0.69125482 0.38926427 0.79092135 0.38346405 0.22875840 0.97854737
    ##  [7] 0.19221509 0.17662746 0.70092791 0.03548995

``` r
# use [[
head(unlist(Map(`[[`, trials, "p.value")), 10)
```

    ##  [1] 0.69125482 0.38926427 0.79092135 0.38346405 0.22875840 0.97854737
    ##  [7] 0.19221509 0.17662746 0.70092791 0.03548995

#### 4. What does replicate() do? What sort of for loop does it eliminate? Why do its arguments differ from lapply() and friends?

replicate is a wrapper for the common use of sapply for repeated evaluation of an expression (which will usually involve random number generation).

It eliminates the loop over the numeric indices: for (i in n)

Only replicate has the argument of expr, which is the expression (a language object, usually a call) to evaluate repeatedly.

#### 5. Implement a version of lapply() that supplies FUN with both the name and the value of each component.

Create function

``` r
lapply2 <- function(x1, x2, FUN, ...){
  out <- vector("list", length(x1))
  for (i in seq_along(x1)) {
    out[[i]] <- weighted.mean(x1[[i]], x2[[i]])
  }
  out
}
xs <- replicate(5, runif(10), simplify = FALSE)
ws <- replicate(5, rpois(10, 5) + 1, simplify = FALSE)
```

Test function and check with Map()

``` r
unlist(lapply2(xs, ws, weighted.mean))
```

    ## [1] 0.3933382 0.3575627 0.2974574 0.5799151 0.6796087

``` r
unlist(Map(weighted.mean, xs, ws))
```

    ## [1] 0.3933382 0.3575627 0.2974574 0.5799151 0.6796087

#### 6. Implement a combination of Map() and vapply() to create an lapply() variant that iterates in parallel over all of its inputs and stores its outputs in a vector (or a matrix). What arguments should the function take?

Create function

``` r
lapply3 <- function (f, mc.cores = 1L, FUN.VALUE = character(1), ...) 
{
    cores <- as.integer(mc.cores)
    if (cores < 1L) 
        stop("'mc.cores' must be >= 1")
    if (cores > 1L) 
        stop("'mc.cores' > 1 is not supported on Windows")
    vapply(Map(f, ...), function(x) x, FUN.VALUE = FUN.VALUE)
}
```

Test function (mc.core must be exactly 1 on Windows (which uses the master process))

``` r
lapply3(xs, ws, f = weighted.mean, mc.cores = 1L, FUN.VALUE = numeric(1))
```

    ## [1] 0.3933382 0.3575627 0.2974574 0.5799151 0.6796087

#### 7. Implement mcsapply(), a multicore version of sapply(). Can you implement mcvapply(), a parallel version of vapply()? Why or why not?

Create function mcsapply

``` r
mcsapply <- function (X, FUN, ..., mc.preschedule = TRUE, mc.set.seed = TRUE, 
    mc.silent = FALSE, mc.cores = 1L, mc.cleanup = TRUE, mc.allow.recursive = TRUE, 
    affinity.list = NULL) 
{
    cores <- as.integer(mc.cores)
    if (cores < 1L) 
        stop("'mc.cores' must be >= 1")
    if (cores > 1L) 
        stop("'mc.cores' > 1 is not supported on Windows")
    sapply(X, FUN, ...)
}
```

Test function

``` r
mcsapply(mtcars, mean)
```

    ##        mpg        cyl       disp         hp       drat         wt 
    ##  20.090625   6.187500 230.721875 146.687500   3.596563   3.217250 
    ##       qsec         vs         am       gear       carb 
    ##  17.848750   0.437500   0.406250   3.687500   2.812500

Technically we could also create mcvapply. However, my test case is limited to windows.

``` r
mcvapply <- function (X, FUN, ..., mc.preschedule = TRUE, mc.set.seed = TRUE, 
    mc.silent = FALSE, mc.cores = 1L, mc.cleanup = TRUE, mc.allow.recursive = TRUE, 
    affinity.list = NULL) 
{
    cores <- as.integer(mc.cores)
    if (cores < 1L) 
        stop("'mc.cores' must be >= 1")
    if (cores > 1L) 
        stop("'mc.cores' > 1 is not supported on Windows")
    vapply(X, FUN, ...)
}

mcvapply(mtcars, class, character(1))
```

    ##       mpg       cyl      disp        hp      drat        wt      qsec 
    ## "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" 
    ##        vs        am      gear      carb 
    ## "numeric" "numeric" "numeric" "numeric"

Manipulating matrices and data frames
-------------------------------------

#### 1. How does apply() arrange the output? Read the documentation and perform some experiments.

If each call to FUN returns a vector of length n, then apply returns an array of dimension c(n, dim(X)\[MARGIN\]) if n &gt; 1. If n equals 1, apply returns a vector if MARGIN has length 1 and an array of dimension dim(X)\[MARGIN\] otherwise. If n is 0, the result has length 0 but not necessarily the 'correct' dimension.

If the calls to FUN return vectors of different lengths, apply returns a list of length prod(dim(X)\[MARGIN\]) with dim set to MARGIN if this has length greater than one.

``` r
# When n > 1, return an array of dimension c(n, dim(X)[MARGIN])
dim(apply(mtcars, 2, function(x) c(mean(x), median(x))))
```

    ## [1]  2 11

``` r
# When n = 1, return a vector (no dimension)
dim(apply(mtcars, 2, mean))
```

    ## NULL

``` r
# When n = 0, the result has length 0
length(apply(mtcars, 2, function(x) NULL))
```

    ## [1] 0

``` r
# When returning vectors of different lengths 
# (example from R documentation)
z <- array(1:24, dim = 2:4)
zseq <- apply(z, 1:2, function(x) seq_len(max(x)))
apply(z, 3, function(x) seq_len(max(x)))
```

    ## [[1]]
    ## [1] 1 2 3 4 5 6
    ## 
    ## [[2]]
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12
    ## 
    ## [[3]]
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18
    ## 
    ## [[4]]
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
    ## [24] 24

#### 2. There's no equivalent to split() + vapply(). Should there be? When would it be useful? Implement one yourself.

There should be a equivalent function of split() + vapply() as it could be useful when this kind of function needs to always generate desirable outputs in cases such as implemention in another function.

``` r
# tapply throughs an error when one of the output has lenght zero
pulse <- c(round(rnorm(22, 70, 10 / 3)) + rep(c(0, 5), c(10, 12)), NULL)
group <- c(rep(c("A", "B"), c(10, 12)), "C")
try(tapply(pulse, group, length))

# Implement split() + vapply() (warning is muted)
tapply2 <- function(x, group, f, FUN.VALUE, ...) {
  pieces <- split(x, group)
  vapply(pieces, f, FUN.VALUE)
}
tapply2(pulse, group, length, numeric(1))
```

    ##  A  B  C 
    ## 10 12  0

#### 3. Implement a pure R version of split(). (Hint: use unique() and subsetting.) Can you do it without a for loop?

Create function

``` r
split2 <- function(x, group){
  g <- unique(group)
  out <- lapply(g, function(g) x[group == g])
  setNames(out, g)
}
```

Test function

``` r
pulse <- c(round(rnorm(22, 70, 10 / 3)) + rep(c(0, 5), c(10, 12)))
group <- c(rep(c("A", "B"), c(10, 12)))
split2(pulse, group)
```

    ## $A
    ##  [1] 73 74 71 69 67 76 73 70 72 73
    ## 
    ## $B
    ##  [1] 72 72 77 75 75 74 74 76 77 76 73 72

#### 4. What other types of input and output are missing? Brainstorm before you look up some answers in the plyr paper.

Output: Discarded (\*\_ply)

Sometimes it is convenient to operate on a list purely for the side eects, e.g., plots, caching, and output to screen/le. In this case \*\_ply is a little more ecient than abandoning the output of *lply because it does not store the intermediate results. The *\_ply functions have one additional argument, .print, which controls whether or not each result should be printed. This is useful when working with lattice (Sarkar 2008) or ggplot2 (Wickham 2010) graphics.

Reference: The Split-Apply-Combine Strategy for Data Analysis (<https://www.jstatsoft.org/article/view/v040i01>)

Manipulating lists
------------------

#### 1. Why isn't is.na() a predicate function? What base R function is closest to being a predicate version of is.na()?

is.na() doesn't return TRUE for a vector of NA.

``` r
list_test <-
  list(a = c(1:5),
       b = replicate(5, NA))
list_test
```

    ## $a
    ## [1] 1 2 3 4 5
    ## 
    ## $b
    ## [1] NA NA NA NA NA

``` r
is.na(list_test)
```

    ##     a     b 
    ## FALSE FALSE

To return TRUE in this case, one option is to combine all() and is.na() from base R functions.

``` r
sapply(list_test, function(x) all(is.na(x)))
```

    ##     a     b 
    ## FALSE  TRUE

#### 2. Use Filter() and vapply() to create a function that applies a summary statistic to every numeric column in a data frame.

Create function

``` r
summary2 <- function(df){
  vapply(Filter(is.numeric, df), summary, FUN.VALUE = numeric(6))
}
```

Test function (vs and am are factors)

``` r
summary2(mtcars2)
```

    ##              mpg    cyl     disp       hp     drat      wt     qsec   gear
    ## Min.    10.40000 4.0000  71.1000  52.0000 2.760000 1.51300 14.50000 3.0000
    ## 1st Qu. 15.42500 4.0000 120.8250  96.5000 3.080000 2.58125 16.89250 3.0000
    ## Median  19.20000 6.0000 196.3000 123.0000 3.695000 3.32500 17.71000 4.0000
    ## Mean    20.09062 6.1875 230.7219 146.6875 3.596563 3.21725 17.84875 3.6875
    ## 3rd Qu. 22.80000 8.0000 326.0000 180.0000 3.920000 3.61000 18.90000 4.0000
    ## Max.    33.90000 8.0000 472.0000 335.0000 4.930000 5.42400 22.90000 5.0000
    ##           carb
    ## Min.    1.0000
    ## 1st Qu. 2.0000
    ## Median  2.0000
    ## Mean    2.8125
    ## 3rd Qu. 4.0000
    ## Max.    8.0000

#### 3. What's the relationship between which() and Position()? What's the relationship between where() and Filter()?

Position() is equivalent to:

``` r
function(x) which(x)[1]
```

which() returns the opsition of all the lements that match the predicate, while Position() returns the position of the first element that matches the predicate (or the last element if right = TRUE).

Filter() could be considered as:

``` r
function(f, x) x[where(f, x)]
```

where() returns a logical vector of TRUE and FALSE indicating whether the element matchs the preicate , but Filter() selects those elements which match the predicate.

#### 4. Implement Any(), a function that takes a list and a predicate function, and returns TRUE if the predicate function returns TRUE for any of the inputs. Implement All() similarly.

Create function

``` r
where <- function(f, x) {
  vapply(x, f, logical(1))
}
Any <- function(f, x){
  any(where(f, x))
}
All <- function(f, x){
  all(where(f, x))
}
```

Test function

``` r
Any(is.numeric, mtcars2)
```

    ## [1] TRUE

``` r
All(is.numeric, mtcars2)
```

    ## [1] FALSE

``` r
All(is.numeric, mtcars)
```

    ## [1] TRUE

#### 5. Implement the span() function from Haskell: given a list x and a predicate function f, span returns the location of the longest sequential run of elements where the predicate is true. (Hint: you might find rle() helpful.)

Create function

``` r
span <- function(f, x){
  y <- unname(vapply(x, f, logical(1)))
  sq <- rle(y)
  lengthsTrue <- sq$lengths[sq$values]
  positions <- cumsum(sq$lengths)
  positionsTrue <- positions[sq$values]
  longest_seq <- max(lengthsTrue)
  longest_position <- Position(function(x) x == longest_seq, lengthsTrue)
  # senario 1
  if (longest_position == 1 & sq$values[1]){
    starting <- 1
  # senario 2
  } else if (longest_position == 1 & !sq$values[1]) {
    starting <- positionsTrue[1] - 1
  # senario 3
  } else {
    starting <- positionsTrue[longest_position - 1] + 1
  }
  return(seq(starting, starting + longest_seq - 1))
}
```

Test function

``` r
vapply(mtcars2, is.factor, logical(1))
```

    ##   mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb 
    ## FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE FALSE FALSE

``` r
span(is.numeric, mtcars2) # senario 1
```

    ## [1] 1 2 3 4 5 6 7

``` r
span(is.factor, mtcars2) # senario 2
```

    ## [1] 8 9

``` r
span(is.numeric, mtcars2[c("qsec", "vs", "am", "gear", "carb")]) # senario 3
```

    ## [1] 2 3

Mathematical functionals
------------------------

Reference for the negative log likelihood (NLL) closure in the lecture:

-   <https://www.statlect.com/fundamentals-of-statistics/Poisson-distribution-maximum-likelihood>

#### 1. Implement arg\_max(). It should take a function and a vector of inputs, and return the elements of the input where the function returns the highest value. For example, arg\_max(-10:5, function(x) x ^ 2) should return -10. arg\_max(-5:5, function(x) x ^ 2) should return c(-5, 5). Also implement the matching arg\_min() function.

Create function

``` r
arg_max <- function(vec, fun) {
  out <- sapply(vec, fun)
  vec[out == max(out)]
}
```

Test function

``` r
arg_max(-10:5, function(x) x ^ 2)
```

    ## [1] -10

``` r
arg_max(-5:5, function(x) x ^ 2)
```

    ## [1] -5  5

To create arg\_min(), simply change max() into min() inside the arg\_max function.

#### 2. Challenge: read about the fixed point algorithm ([http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html\#%\_sec\_1.3](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3)). Complete the exercises using R.

Link doesn't work.

Case study
----------

#### 1. Implement smaller and larger functions that, given two inputs, return either the smaller or the larger value. Implement na.rm = TRUE: what should the identity be? (Hint: smaller(x, smaller(NA, NA, na.rm = TRUE), na.rm = TRUE) must be x, so smaller(NA, NA, na.rm = TRUE) must be bigger than any other value of x.) Use smaller and larger to implement equivalents of min(), max(), pmin(), pmax(), and new functions row\_min() and row\_max().

Create function for na.rm

``` r
# Create function for na.rm
rm_na <- function(x, y, identity) {
  if (is.na(x) && is.na(y)) {
    identity
  } else if (is.na(x)) {
    y
  } else {
    x
  }
}
```

Create function smaller (equivalent of min)

``` r
smaller <- function(x, y, na.rm = FALSE) {
  stopifnot(length(x) == 1, length(y) == 1)
  if (na.rm && (is.na(x) || is.na(y))) 
    rm_na(x, y, NA)
  else if (is.na(x) || is.na(y))
    NA
  else if (x > y)
    y
  else
    x
}
```

Test smaller

``` r
smaller(1, 2)
```

    ## [1] 1

``` r
smaller(2, NA)
```

    ## [1] NA

``` r
smaller(2, NA, na.rm = TRUE)
```

    ## [1] 2

``` r
smaller(NA, NA, na.rm = TRUE)
```

    ## [1] NA

``` r
smaller(1, smaller(NA, NA, na.rm = TRUE), na.rm = TRUE)
```

    ## [1] 1

Create function larger() (equivalent of max)

``` r
larger <- function(x, y, na.rm = FALSE) {
  stopifnot(length(x) == 1, length(y) == 1)
  if (na.rm && (is.na(x) || is.na(y))) 
    rm_na(x, y, NA)
  else if (is.na(x) || is.na(y))
    NA
  else if (x > y)
    x
  else
    y
}
```

Test larger

``` r
larger(1, 2)
```

    ## [1] 2

``` r
larger(2, NA)
```

    ## [1] NA

``` r
larger(2, NA, na.rm = TRUE)
```

    ## [1] 2

``` r
larger(NA, NA, na.rm = TRUE)
```

    ## [1] NA

``` r
larger(1, larger(NA, NA, na.rm = TRUE), na.rm = TRUE)
```

    ## [1] 1

Create function psmaller() (equivalent of pmin)

``` r
psmaller <- function(x, y, na.rm = TRUE){
  l <- max(length(x), length(y))
  if (length(x) < l) x <- rep(x, l)[seq(l)]
  if (length(y) < l) y <- rep(y, l)[seq(l)]
  vapply(seq(l), 
         function(i) smaller(x[i], y[i], na.rm = na.rm),
         numeric(1))
}
```

Test psmaller

``` r
pmin(5:1, pi)
```

    ## [1] 3.141593 3.141593 3.000000 2.000000 1.000000

``` r
psmaller(5:1, pi)
```

    ## [1] 3.141593 3.141593 3.000000 2.000000 1.000000

``` r
psmaller(pi, 5:1)
```

    ## [1] 3.141593 3.141593 3.000000 2.000000 1.000000

``` r
psmaller(5:1, 1:5)
```

    ## [1] 1 2 3 2 1

``` r
psmaller(5:1, 1:2)
```

    ## [1] 1 2 1 2 1

To create a new function plarger() which is equivalent to pmax(), simply change smaller to larger in psmaller() function

Create a function to find the smallest value in a vector

``` r
rsmaller <- function(xs, na.rm = TRUE) {
  Reduce(function(x, y) smaller(x, y, na.rm = na.rm), xs, init = NA)
}
```

Test rsmaller

``` r
rsmaller(10:1)
```

    ## [1] 1

Create new function row\_min()

``` r
row_min <- function(x, na.rm = TRUE) {
  apply(x, 1, rsmaller, na.rm = TRUE)
}
```

Test row\_min

``` r
row_min(matrix(1:20, nrow = 4))
```

    ## [1] 1 2 3 4

The same logic of creating row\_min could be applied to create row\_max

#### 2. Create a table that has and, or, add, multiply, smaller, and larger in the columns and binary operator, reducing variant, vectorised variant, and array variants in the rows.

#### a. Fill in the cells with the names of base R functions that perform each of the roles.

#### b. Compare the names and arguments of the existing R functions. How consistent are they? How could you improve them?

#### c. Complete the matrix by implementing any missing functions.

Base R functions in each role (Note: In tte table $ represents |. Due to formatting issue, | will be considered as part of table boarders, so I used the symple $ instead.)

|                    | And | Or  | Add | Multiply | Smaller | Larger |
|--------------------|-----|-----|-----|----------|---------|--------|
| Binary operator    | &   | $   | +   | \*       | min     | max    |
| Reducing variant   | all | any | sum | prod     | min     | max    |
| Vectorised variant | &   | $   | +   | \*       | pmin    | pmax   |
| Array variant      | &   | $   | +   | \*       | N.A.    | N.A.   |

R is very handy and consistent in using 'and', 'or', add, and multiply to between scalar, vector, and arrays. Depending on the input, the output could always return the same dimension. For example, c(TRUE, TRUE) & c(TRUE, FALSE) returns (TRUE FALS)E, and (boolean matrix) & (boolean matrix) returns a boolean matrix.

The naming of reducing cases is distinct from others as it is closer to mathematical terms.

Although not highly consistent, these functions could becom intuitive after familiarizing with R.

Create function amin for smaller in array variant

``` r
amin <- function(x, y, na.rm = TRUE){
  # get dimension
  if (is.null(dim(x))) xd <- 0 else xd <- dim(x)
  if (is.null(dim(y))) yd <- 0 else yd <- dim(y)
  d <- pmax(xd, yd)
  if (!identical(d, xd)) x <- array(c(x), d)
  if (!identical(d, yd)) y <- array(c(y), d)
  # flat matrix to vector
  # vapply
  out <- vapply(seq(prod(d)),
         function(i) smaller(c(x)[i], c(y)[i], na.rm = na.rm),
         numeric(1))
  matrix(out, d)
}
```

Test function

``` r
amin(matrix(1:6, nrow = 2), matrix(rep(3,6), nrow = 2))
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    3
    ## [2,]    2    3    3

``` r
amin(matrix(1:6, nrow = 2), matrix(rep(3,9), nrow = 3))
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    1
    ## [2,]    2    3    2
    ## [3,]    3    3    3

``` r
amin(c(1:6), matrix(rep(3,9), nrow = 3))
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    1
    ## [2,]    2    3    2
    ## [3,]    3    3    3

The same logic could be applied to create larger in array variant

#### 3. How does paste() fit into this structure? What is the scalar binary function that underlies paste()? What are the sep and collapse arguments to paste() equivalent to? Are there any paste variants that don't have existing R implementations?

Fit paste() into the structure

|                    | paste  |
|--------------------|--------|
| Binary operator    | paste0 |
| Reducing variant   | paste  |
| Vectorised variant | paste0 |
| Array variant      | N.A.   |

The sep argument is equivalent to paste one more scalar or vector object between the each pair of the inputs. The collapse argument is equivalent to reducing the result to an output of length 1 or not.

It seems the array variant doesn't have existing R implementations.

Play around with some examples.

``` r
a <- paste(1, 2)
b <- paste(c(1,2), c(3,4))
b1 <- paste0(c(1,2), c(3,4))
b2 <- paste(c(1,2), c(3,4), sep ="", collapse="")
c <- paste(matrix(1:4, nrow = 2), matrix(5:8, nrow = 2))

# binary operator with " "
a
```

    ## [1] "1 2"

``` r
length(a)
```

    ## [1] 1

``` r
# vectorized variant with " "
b
```

    ## [1] "1 3" "2 4"

``` r
length(b)
```

    ## [1] 2

``` r
# vectorized variant without " "
b1
```

    ## [1] "13" "24"

``` r
length(b1)
```

    ## [1] 2

``` r
# reducing variant
b2
```

    ## [1] "1324"

``` r
length(b2)
```

    ## [1] 1

``` r
# not applicable to array variant
c
```

    ## [1] "1 5" "2 6" "3 7" "4 8"

``` r
dim(c)
```

    ## NULL
