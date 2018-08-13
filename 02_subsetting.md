Subsetting
================
Byron Tang
May 14, 2018

Data Type
---------

#### 1. Fix each of the following common data frame subsetting errors:

``` r
mtcars[mtcars$cyl = 4, ]
mtcars[-1:4, ]
mtcars[mtcars$cyl <= 5]
mtcars[mtcars$cyl == 4 | 6, ]
```

Fix the codes

``` r
mtcars[mtcars$cyl == 4,]
```

    ##                 mpg cyl  disp  hp drat    wt  qsec vs am gear carb
    ## Datsun 710     22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
    ## Merc 240D      24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
    ## Merc 230       22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
    ## Fiat 128       32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
    ## Honda Civic    30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
    ## Toyota Corolla 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
    ## Toyota Corona  21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
    ## Fiat X1-9      27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
    ## Porsche 914-2  26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
    ## Lotus Europa   30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
    ## Volvo 142E     21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2

``` r
mtcars[seq(nrow(mtcars), nrow(mtcars) - 3), ]
```

    ##                 mpg cyl disp  hp drat   wt qsec vs am gear carb
    ## Volvo 142E     21.4   4  121 109 4.11 2.78 18.6  1  1    4    2
    ## Maserati Bora  15.0   8  301 335 3.54 3.57 14.6  0  1    5    8
    ## Ferrari Dino   19.7   6  145 175 3.62 2.77 15.5  0  1    5    6
    ## Ford Pantera L 15.8   8  351 264 4.22 3.17 14.5  0  1    5    4

``` r
mtcars[mtcars$cyl <= 5,]
```

    ##                 mpg cyl  disp  hp drat    wt  qsec vs am gear carb
    ## Datsun 710     22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
    ## Merc 240D      24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
    ## Merc 230       22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
    ## Fiat 128       32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
    ## Honda Civic    30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
    ## Toyota Corolla 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
    ## Toyota Corona  21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
    ## Fiat X1-9      27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
    ## Porsche 914-2  26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
    ## Lotus Europa   30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
    ## Volvo 142E     21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2

``` r
mtcars[mtcars$cyl == 4 | mtcars$cyl == 6, ]
```

    ##                 mpg cyl  disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4      21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag  21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710     22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive 21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
    ## Valiant        18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1
    ## Merc 240D      24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
    ## Merc 230       22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
    ## Merc 280       19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
    ## Merc 280C      17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
    ## Fiat 128       32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
    ## Honda Civic    30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
    ## Toyota Corolla 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
    ## Toyota Corona  21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
    ## Fiat X1-9      27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
    ## Porsche 914-2  26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
    ## Lotus Europa   30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
    ## Ferrari Dino   19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6
    ## Volvo 142E     21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2

#### 2. Why does x &lt;- 1:5; x\[NA\] yield five missing values? (Hint: why is it different from x\[NA\_real\_\]?)

NA is a logical vector of lenght one. When the logical vector is shorter than the vector being subsetted, it will be recycled to be the same length. On the other hand, NA\_real\_ is a double datatype, so it is not recycled and only retrieves one value.

``` r
x <- 1:5
x[NA]
```

    ## [1] NA NA NA NA NA

``` r
x[NA_real_]
```

    ## [1] NA

``` r
x[as.logical(NA_real_)]
```

    ## [1] NA NA NA NA NA

#### 3. What does upper.tri() return? How does subsetting a matrix with it work? Do we need any additional subsetting rules to describe its behaviour?

``` r
x <- outer(1:5, 1:5, FUN = "*")
x[upper.tri(x)]
```

Run the codes

``` r
x <- outer(1:5, 1:5, FUN = "*")
x
```

    ##      [,1] [,2] [,3] [,4] [,5]
    ## [1,]    1    2    3    4    5
    ## [2,]    2    4    6    8   10
    ## [3,]    3    6    9   12   15
    ## [4,]    4    8   12   16   20
    ## [5,]    5   10   15   20   25

upper.tri() returns the upper triangle of matrix x excluding the diagnal.

``` r
x[upper.tri(x)]
```

    ##  [1]  2  3  6  4  8 12  5 10 15 20

#### 4. Why does mtcars\[1:20\] return an error? How does it differ from the similar mtcars\[1:20, \]?

mtcars is a data.frame object and not a vector, so we could not use \[1:20\] to subset it. We should use subset it like either a list or a matrix. mtcars\[1, \] is the way to subset like a matrix.

``` r
class(mtcars)
```

    ## [1] "data.frame"

#### 5. Implement your own function that extracts the diagonal entries from a matrix (it should behave like diag(x) where x is a matrix).

``` r
x <- outer(1:5, 1:5, FUN = paste, sep = ",")
x
```

    ##      [,1]  [,2]  [,3]  [,4]  [,5] 
    ## [1,] "1,1" "1,2" "1,3" "1,4" "1,5"
    ## [2,] "2,1" "2,2" "2,3" "2,4" "2,5"
    ## [3,] "3,1" "3,2" "3,3" "3,4" "3,5"
    ## [4,] "4,1" "4,2" "4,3" "4,4" "4,5"
    ## [5,] "5,1" "5,2" "5,3" "5,4" "5,5"

``` r
get_matrix_diagnal <- function(a_matrix){
  if (is.matrix(a_matrix)){
    num <- min(nrow(x), ncol(x))
    diagnal <- cbind(1:num, 1:num)
    return(x[diagnal])
  } else {
    return("Please pass a matrix")
  }
}
cat("\n")
```

``` r
get_matrix_diagnal(x)
```

    ## [1] "1,1" "2,2" "3,3" "4,4" "5,5"

``` r
get_matrix_diagnal(mtcars)
```

    ## [1] "Please pass a matrix"

#### 6. What does df\[is.na(df)\] &lt;- 0 do? How does it work?

The command would assign 0 to all the NA in the df

Note: need to set stringAsFactors = FALSE, otherwise the command would throw an error.

``` r
df <- data.frame(a = c(1, NA, 3),
                 b = c("x", "y", NA),
                 stringsAsFactors = FALSE)
df
```

    ##    a    b
    ## 1  1    x
    ## 2 NA    y
    ## 3  3 <NA>

``` r
df[is.na(df)] <- 0
df
```

    ##   a b
    ## 1 1 x
    ## 2 0 y
    ## 3 3 0

Subsetting Operators
--------------------

#### 1. Given a linear model, e.g., mod &lt;- lm(mpg ~ wt, data = mtcars), extract the residual degrees of freedom. Extract the R squared from the model summary (summary(mod))

``` r
mod <- lm(mpg ~ wt, data = mtcars)
attributes(mod)
```

    ## $names
    ##  [1] "coefficients"  "residuals"     "effects"       "rank"         
    ##  [5] "fitted.values" "assign"        "qr"            "df.residual"  
    ##  [9] "xlevels"       "call"          "terms"         "model"        
    ## 
    ## $class
    ## [1] "lm"

``` r
mod$df.residual
```

    ## [1] 30

``` r
attributes(summary(mod))
```

    ## $names
    ##  [1] "call"          "terms"         "residuals"     "coefficients" 
    ##  [5] "aliased"       "sigma"         "df"            "r.squared"    
    ##  [9] "adj.r.squared" "fstatistic"    "cov.unscaled" 
    ## 
    ## $class
    ## [1] "summary.lm"

``` r
summary(mod)$r.squared
```

    ## [1] 0.7528328

Subsetting and Assignment/ Applications
---------------------------------------

Notes: Lookup tables

``` r
x <- c("m", "f", "u", "f", "f", "m", "m")
lookup <- c(m = "Male", f = "Female", u = NA)
lookup[x]
```

    ##        m        f        u        f        f        m        m 
    ##   "Male" "Female"       NA "Female" "Female"   "Male"   "Male"

``` r
unname(lookup[x])
```

    ## [1] "Male"   "Female" NA       "Female" "Female" "Male"   "Male"

Notes: Matching and merging

``` r
grades <- c(1, 2, 2, 3, 1)

info <- data.frame(
  grade = 3:1,
  desc = c("Excellent", "Good", "Poor"),
  fail = c(F, F, T)
)

# Use match
id <- match(grades, info$grade)
info[id, ]
```

    ##     grade      desc  fail
    ## 3       1      Poor  TRUE
    ## 2       2      Good FALSE
    ## 2.1     2      Good FALSE
    ## 1       3 Excellent FALSE
    ## 3.1     1      Poor  TRUE

``` r
# Using rownames
rownames(info) <- info$grade
info[as.character(grades), ]
```

    ##     grade      desc  fail
    ## 1       1      Poor  TRUE
    ## 2       2      Good FALSE
    ## 2.1     2      Good FALSE
    ## 3       3 Excellent FALSE
    ## 1.1     1      Poor  TRUE

#### 1. How would you randomly permute the columns of a data frame? (This is an important technique in random forests.) Can you simultaneously permute the rows and columns in one step?

``` r
df <- data.frame(x = c(1:5),
                 y = letters[1:5],
                 z = tail(LETTERS, 5),
                 stringsAsFactors = FALSE)


df[,sample(colnames(df), 5, replace = TRUE)]
```

    ##   z z.1 z.2 y z.3
    ## 1 V   V   V a   V
    ## 2 W   W   W b   W
    ## 3 X   X   X c   X
    ## 4 Y   Y   Y d   Y
    ## 5 Z   Z   Z e   Z

``` r
df[sample(nrow(df), 10, replace = TRUE) ,sample(colnames(df), 5, replace = TRUE)]
```

    ##     y z x z.1 z.2
    ## 2   b W 2   W   W
    ## 1   a V 1   V   V
    ## 3   c X 3   X   X
    ## 3.1 c X 3   X   X
    ## 5   e Z 5   Z   Z
    ## 3.2 c X 3   X   X
    ## 4   d Y 4   Y   Y
    ## 3.3 c X 3   X   X
    ## 5.1 e Z 5   Z   Z
    ## 1.1 a V 1   V   V

#### 2. How would you select a random sample of m rows from a data frame? What if the sample had to be contiguous (i.e., with an initial row, a final row, and every row in between)?

``` r
df <- data.frame(x = c(1:20),
                 y = letters[1:20],
                 z = tail(LETTERS, 20),
                 stringsAsFactors = FALSE)

# m must be less than the number of rows in the target data frame
m <- 5
initial_row <- sample(nrow(df) - m, 1)
df[seq(initial_row, initial_row+m-1),]
```

    ##     x y z
    ## 11 11 k Q
    ## 12 12 l R
    ## 13 13 m S
    ## 14 14 n T
    ## 15 15 o U

#### 3. How could you put the columns in a data frame in alphabetical order?

``` r
df <- data.frame(k = c(1:5),
                 a = letters[1:5],
                 f = tail(LETTERS, 5),
                 stringsAsFactors = FALSE)

df[order(colnames(df))]
```

    ##   a f k
    ## 1 a V 1
    ## 2 b W 2
    ## 3 c X 3
    ## 4 d Y 4
    ## 5 e Z 5
