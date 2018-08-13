Functions
================
Byron Tang
May 19, 2018

Function components
-------------------

#### 1. What function allows you to tell if an object is a function? What function allows you to tell if a function is a primitive function?

``` r
is.function(sum)
```

    ## [1] TRUE

``` r
is.primitive(sum)
```

    ## [1] TRUE

``` r
is.primitive(summary)
```

    ## [1] FALSE

#### 2. This code makes a list of all functions in the base package.

``` r
objs <- mget(ls("package:base"), inherits = TRUE)
funs <- Filter(is.function, objs)
```

#### Use it to answer the following questions:

#### a. Which base function has the most arguments?

#### b. How many base functions have no arguments? What's special about those functions?

#### c. How could you adapt the code to find all primitive functions?

Run the codes

``` r
objs <- mget(ls("package:base"), inherits = TRUE)
funs <- Filter(is.function, objs) # 1207 functions
```

a

``` r
funs_arg <- sapply(lapply(names(funs), formals), length)
(most_arg <- names(funs[funs_arg == max(funs_arg)]))
```

    ## [1] "scan"

``` r
length(formals(most_arg))
```

    ## [1] 22

b: Most of the functions wihtout arguments are primitive

``` r
no_arg <- funs[funs_arg == 0]
length(no_arg)
```

    ## [1] 224

``` r
no_arg_primitive <- sapply(no_arg, is.primitive)
sum(no_arg_primitive)
```

    ## [1] 183

c: 183 primitive functions

``` r
funs_primitive <- Filter(is.primitive, objs)
```

#### 3. What are the three important components of a function?

1.  the body(), the code inside the function.
2.  the formals(), the list of arguments which controls how you can call the function.
3.  the environment(), the "map" of the location of the function's variables.

#### 4. When does printing a function not show what environment it was created in?

If the environment isn't displayed, it means that the function was created in the global environment.

Lexical Scoping
---------------

#### 1. What does the following code return? Why? What does each of the three c's mean?

``` r
c <- 10
c(c = c)
```

It returns a vector of lenght 1 with value 10 which has a name c. The function looks up to find out an object c, gets the value that c binds to, and then assigns the value with a name c.

The c in c &lt;- 10 binds to a value 10 In c(c = c): - The first c is a function - The second c is a name created in the function - The third c is the c in c &lt;- 10 that binds to a value 10

``` r
# Run the codes
c <- 10
c(c = c)
```

    ##  c 
    ## 10

#### 2. What are the four principles that govern how R looks for values?

1.  name masking
2.  functions vs. variables
3.  a fresh start
4.  dynamic lookup

#### 3. What does the following function return? Make a prediction before running the code yourself.

``` r
f <- function(x) {
  f <- function(x) {
    f <- function(x) {
      x ^ 2
    }
    f(x) + 1
  }
  f(x) * 2
}
f(10)
```

(10 ^ 2 + 1) \* 2 = 202 Run the codes

``` r
f <- function(x) {
  f <- function(x) {
    f <- function(x) {
      x ^ 2
    }
    f(x) + 1
  }
  f(x) * 2
}
f(10)
```

    ## [1] 202

Function arguments
------------------

#### 1. Clarify the following list of odd function calls:

``` r
x <- sample(replace = TRUE, 20, x = c(1:10, NA))
y <- runif(min = 0, max = 1, 20)
cor(m = "k", y = y, u = "p", x = x)
```

Clarify and run the codes

``` r
x <- sample(c(1:10, NA), 20, replace = TRUE)
y <- runif(20, 0, 1)
cor(x, y, use = "pairwise.complete.obs", method = "kendall")
```

    ## [1] 0.2464913

#### 2. What does this function return? Why? Which principle does it illustrate?

``` r
f1 <- function(x = {y <- 1; 2}, y = 0) {
  x + y
}
f1()
```

f1 returns 3 because of lazy evaluation:

x in x + y is executed first. When getting x, y is also assigned as 1, making it unneccessary to execute y = 0. Therefore, the output is 2 + 1 = 3

The code is slightly revised to grab the value of x and y

``` r
f1 <- function(x = {y <- 1; 2}, y = 0) {
  print(x) + print(y)
}
f1()
```

    ## [1] 2
    ## [1] 1

    ## [1] 3

To vefity the thought process, change the order of x and y and observe. The result shows that when y is executed first, y = 0 is captured and y &lt;- 0 is ignored.

``` r
f2 <- function(x = {y <- 1; 2}, y = 0) {
  print(y) + print(2)
}
f2()
```

    ## [1] 0
    ## [1] 2

    ## [1] 2

#### 3. What does this function return? Why? Which principle does it illustrate?

``` r
f2 <- function(x = z) {
  z <- 100
  x
}
f2()
```

The function returns 100. Default arguments are evaluated inside the function. The expression depends on the current environment.

Even when there is another global variable z exists, x still captures the z inside the function.

``` r
z <- 50
f2 <- function(x = z) {
  z <- 100
  x
}
f2()
```

    ## [1] 100

Sepcial calls
-------------

#### 1. Create a list of all the replacement functions found in the base package. Which ones are primitive functions?

``` r
# objs <- mget(ls("package:base"), inherits = TRUE)
is_replacement <- grepl("<-", names(objs))
sum(is_replacement)
```

    ## [1] 60

``` r
funs_replacement <- objs[is_replacement]
funs_replacement[sapply(funs_replacement, is.primitive)]
```

    ## $`$<-`
    ## .Primitive("$<-")
    ## 
    ## $`@<-`
    ## .Primitive("@<-")
    ## 
    ## $`[[<-`
    ## .Primitive("[[<-")
    ## 
    ## $`[<-`
    ## .Primitive("[<-")
    ## 
    ## $`<-`
    ## .Primitive("<-")
    ## 
    ## $`<<-`
    ## .Primitive("<<-")
    ## 
    ## $`attr<-`
    ## function (x, which, value)  .Primitive("attr<-")
    ## 
    ## $`attributes<-`
    ## function (obj, value)  .Primitive("attributes<-")
    ## 
    ## $`class<-`
    ## function (x, value)  .Primitive("class<-")
    ## 
    ## $`dim<-`
    ## function (x, value)  .Primitive("dim<-")
    ## 
    ## $`dimnames<-`
    ## function (x, value)  .Primitive("dimnames<-")
    ## 
    ## $`environment<-`
    ## function (fun, value)  .Primitive("environment<-")
    ## 
    ## $`length<-`
    ## function (x, value)  .Primitive("length<-")
    ## 
    ## $`levels<-`
    ## function (x, value)  .Primitive("levels<-")
    ## 
    ## $`names<-`
    ## function (x, value)  .Primitive("names<-")
    ## 
    ## $`oldClass<-`
    ## function (x, value)  .Primitive("oldClass<-")
    ## 
    ## $`storage.mode<-`
    ## function (x, value)  .Primitive("storage.mode<-")

#### 2. What are valid names for user-created infix functions?

All user-created infix functions must start and end with % Ex: %custom%

#### 3. Create an infix xor() operator.

Create new function Borrow the code from the source code of xor

``` r
`%x||%` <- function(x, y) {
  (x | y) & !(x & y)
}
```

Test function

``` r
TRUE %x||% TRUE == xor(TRUE, TRUE)
```

    ## [1] TRUE

``` r
TRUE %x||% FALSE == xor(TRUE, FALSE)
```

    ## [1] TRUE

``` r
FALSE %x||% FALSE == xor(FALSE, FALSE)
```

    ## [1] TRUE

#### 4. Create infix versions of the set functions intersect(), union(), and setdiff().

Create new functions The body of the following infix functions are the same with that of the orginal functions

``` r
`%i%` <- function(x, y){
  y <- as.vector(y)
  unique(y[match(as.vector(x), y, 0L)])
}

`%u%` <- function(x, y){
  unique(c(as.vector(x), as.vector(y)))
}

`%s%` <- function(x, y){
  x <- as.vector(x)
  y <- as.vector(y)
  unique(if (length(x) || length(y)) 
      x[match(x, y, 0L) == 0L]
  else x)
}
```

Test functions

``` r
x <- c(1:5)
y <- c(3:7)

x %i% y
```

    ## [1] 3 4 5

``` r
x %u% y
```

    ## [1] 1 2 3 4 5 6 7

``` r
x %s% y
```

    ## [1] 1 2

#### 5. Create a replacement function that modifies a random location in a vector.

Create new function

``` r
`r_replacement<-` <- function(x, value) {
  x[sample(length(x), 1)] <- value
  x
}
```

Run the test 5 times

``` r
for (i in 1:5) {
  x <- 1:10
  r_replacement(x) <- 5L
  print(x)
}
```

    ##  [1]  1  2  3  4  5  6  7  8  5 10
    ##  [1]  1  5  3  4  5  6  7  8  9 10
    ##  [1]  1  2  3  4  5  6  7  8  9 10
    ##  [1] 1 2 3 4 5 6 7 8 9 5
    ##  [1]  1  2  3  4  5  6  7  5  9 10

Return values
-------------

Note: on.exist()

The code in on.exit() is run regardless of how the function exits, whether with an explicit (early) return, an error, or simply reaching the end of the function body.

#### 1. How does the chdir parameter of source() compare to in\_dir()? Why might you prefer one approach to the other?

chdir: logical; if TRUE and file is a pathname, the R working directory is temporarily changed to the directory containing file for evaluating.

1.  Both methods change the directory in the function but restore it after when function is done execution.
2.  I would prefer use chdir as it is a simple argument and saves the work on creating a new function.

#### 2. What function undoes the action of library()? How do you save and restore the values of options() and par()?

detach()

Detach a database, i.e., remove it from the search() path of available R objects. Usually this is either a data.frame which has been attached or a package which was attached by library.

options()

Reference: <https://stackoverflow.com/questions/15946953/how-to-set-r-to-default-options>

``` r
# Save
default_options = options()
save(default_options, file = "default_options.rda")

# Restore
load("default_options.rda")
options(default_options)
```

par()

Reference: RDocumentation

``` r
# Save
op <- par(mfrow = c(2, 2), # 2 x 2 pictures on one plot
          pty = "s")       # square plotting region,
                           # independent of device size

# Restore
## At end of plotting, reset to previous settings:
par(op)
```

#### 3. Write a function that opens a graphics device, runs the supplied code, and closes the graphics device (always, regardless of whether or not the plotting code worked).

``` r
graphIt <- function(code){
  x11()
  force(code)
  ans <- readline(prompt="Press anykey to turn off graph.")
  dev.off()
}

graphIt(plot(1:10))
```

    ## Press anykey to turn off graph.

    ## png 
    ##   2

#### 4. We can use on.exit() to implement a simple version of capture.output().

``` r
capture.output2 <- function(code) {
  temp <- tempfile()
  on.exit(file.remove(temp), add = TRUE)

  sink(temp)
  on.exit(sink(), add = TRUE)

  force(code)
  readLines(temp)
}

capture.output2(cat("a", "b", "c", sep = "\n"))
## [1] "a" "b" "c"
```

#### Compare capture.output() to capture.output2(). How do the functions differ? What features have I removed to make the key ideas easier to see? How have I rewritten the key ideas to be easier to understand?

The input of capture.outpu2() is much more limited. For example: capture.output2 only accepts codes and does not accept a file link nor a list object

The features removed include: - write output into the file - append output into a fil - capture output from a list

The simplified codes in capture.output2 show how capture.output opens a file in the function and then close it at the end of function execution by replacing a file link as a temp file and removing the temp file instead of closing the file passed into the function.

``` r
capture.output2 <- function(code) {
  temp <- tempfile()
  on.exit(file.remove(temp), add = TRUE)

  sink(temp)
  on.exit(sink(), add = TRUE)

  force(code)
  readLines(temp)
}
```

``` r
capture.output(cat("a", "b", "c", sep = "\n"))
```

    ## [1] "a" "b" "c"

``` r
capture.output2(cat("a", "b", "c", sep = "\n"))
```

    ## Warning in file.remove(temp): cannot remove file 'C:\Users\byron\AppData
    ## \Local\Temp\RtmpgrFvx1\file7588117679eb', reason 'Permission denied'

    ## [1] "a" "b" "c"

Reference to more input/output functions: <https://www.statmethods.net/interface/io.html>
