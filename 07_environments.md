Environments
================
Byron Tang
June 2, 2018

Environment basics
------------------

#### 1. List three ways in which an environment differs from a list.

1.  Every name in an environment is unique.
2.  The names in an environment are not ordered (i.e., it does not make sense to ask what the first element of an environment is).
3.  An environment has a parent, except for the empty environment.
4.  Environments have reference semantics.

#### 2. If you don't supply an explicit environment, where do ls() and rm() look? Where does &lt;- make bindings?

ls() and rm() default to the current environment if an environment is not specified.

The operators &lt;- assigns into the environment in which they are evaluated.

#### 3. Using parent.env() and a loop (or a recursive function), verify that the ancestors of globalenv() include baseenv() and emptyenv(). Use the same basic idea to implement your own version of search().

Create function

``` r
rec_env <- function(env){
  # Global environment
  if (is.null(attr(env, "name"))) {
    print(env)
  } else {
  # Environments created by package
    print(attr(env, "name"))
  }
  # Empty environment
  if (identical(env, baseenv())) 
    return(emptyenv())
  return(rec_env(parent.env(env = env)))
}
```

Test function

``` r
search()
```

    ##  [1] ".GlobalEnv"        "package:knitr"     "package:pryr"     
    ##  [4] "package:stats"     "package:graphics"  "package:grDevices"
    ##  [7] "package:utils"     "package:datasets"  "package:methods"  
    ## [10] "Autoloads"         "package:base"

``` r
rec_env(environment())
```

    ## <environment: R_GlobalEnv>
    ## [1] "package:knitr"
    ## [1] "package:pryr"
    ## [1] "package:stats"
    ## [1] "package:graphics"
    ## [1] "package:grDevices"
    ## [1] "package:utils"
    ## [1] "package:datasets"
    ## [1] "package:methods"
    ## [1] "Autoloads"
    ## <environment: base>

    ## <environment: R_EmptyEnv>

### Recursing over environments

#### 1. Modify where() to find all environments that contain a binding for name.

Create function

``` r
where_new <- function(name, env = parent.frame()) {
  # A function that is similar to where() but lives in the function
  # in order to behave differently for objects that exist
  where_in <- function(name, env) {
      # success case
      if (exists(name, envir = env, inherits = FALSE)) {
        print(env)
        # base case
        if (identical(parent.env(env), emptyenv())) {
          return("End of Search")
        } else {
          where_in(name, parent.env(env))
        }
      } else {
        # recursive case
        if (identical(parent.env(env), emptyenv())) {
          return("End of Search")
        } else {
          where_in(name, parent.env(env))
        }
      }
  }
  if (!exists(name, envir = env, inherits = TRUE)) {
    paste("Can't find ", name)
  } else {
    where_in(name, env)
  }
}
```

Create objects and new environments

``` r
# global environment
a <- 1

# global + 1 level of new env
e <- new.env()
e$a <- 2

# global + 2 levels of new env
e2 <- new.env(parent = e)
e2$a <- 3
```

Test

``` r
# Search for 'a' by where function
where("a", env = e2)
```

    ## <environment: 0x000000001c684870>

``` r
# Search for 'a' by new where function
where_new("a", env = e2)
```

    ## <environment: 0x000000001c684870>
    ## <environment: 0x000000001c5c6e60>
    ## <environment: R_GlobalEnv>

    ## [1] "End of Search"

``` r
# Search for 'b'
where_new("b", env = e2)
```

    ## [1] "Can't find  b"

#### 2. Write your own version of get() using a function written in the style of where().

Create function

``` r
get_new <- function(name, env = parent.frame()) {
  if (identical(env, emptyenv())) {
    # Base case
    paste("Can't find ", name)
    
  } else if (exists(name, envir = env, inherits = FALSE)) {
    # Success case
    print(env)
    .Internal(get(name, env, mode = "any", inherits = FALSE))
    
  } else {
    # Recursive case
    get_new(name, parent.env(env))
  }
}
```

Test function

``` r
# Get 'a' with the original get function
get("a", env = e2)
```

    ## [1] 3

``` r
# Get 'a' with the new get function
get_new("a", env = e2)
```

    ## <environment: 0x000000001c684870>

    ## [1] 3

``` r
# Test recursive case in the new get function
c <- 10
get_new("c", env = e2)
```

    ## <environment: R_GlobalEnv>

    ## [1] 10

#### 3. Write a function called fget() that finds only function objects. It should have two arguments, name and env, and should obey the regular scoping rules for functions: if there's an object with a matching name that's not a function, look in the parent. For an added challenge, also add an inherits argument which controls whether the function recurses up the parents or only looks in one environment.

The current fget function has met the criteria of the question

``` r
fget
```

    ## function (name, env = parent.frame()) 
    ## {
    ##     env <- to_env(env)
    ##     if (identical(env, emptyenv())) {
    ##         stop("Could not find function called ", name, call. = FALSE)
    ##     }
    ##     if (exists(name, env, inherits = FALSE) && is.function(env[[name]])) {
    ##         env[[name]]
    ##     }
    ##     else {
    ##         fget(name, parent.env(env))
    ##     }
    ## }
    ## <bytecode: 0x000000001b594c10>
    ## <environment: namespace:pryr>

Create a new function with an inherits argument

``` r
fget_new <- function (name, env = parent.frame(), inherits = TRUE) 
{
    #env <- to_env(env)
    if (inherits) {
      if (identical(env, emptyenv())) {
          stop("Could not find function called ", name, call. = FALSE)
      }
      if (exists(name, env, inherits = FALSE) && is.function(env[[name]])) {
          env[[name]]
      }
      else {
          fget_new(name, parent.env(env))
      }
    } else {
      if (exists(name, env, inherits = FALSE) && is.function(env[[name]])) {
          env[[name]]
      }
      else {
          paste("Counld not find function called", name)
      }
    }
}
```

Test function

``` r
fget("sum")
```

    ## function (..., na.rm = FALSE)  .Primitive("sum")

``` r
fget_new("sum")
```

    ## function (..., na.rm = FALSE)  .Primitive("sum")

``` r
fget_new("sum", inherits = FALSE)
```

    ## [1] "Counld not find function called sum"

#### 4. Write your own version of exists(inherits = FALSE) (Hint: use ls().) Write a recursive version that behaves like exists(inherits = TRUE).

Create function

``` r
exists_new <- function(name, env = parent.frame(), inherits = FALSE) {
    if (inherits) {
      if (identical(env, emptyenv())) {
          return(FALSE)
      }
      if (exists(name, env, inherits = FALSE)) {
          return(TRUE)
      }
      else {
          exists_new(name, parent.env(env), inherits = TRUE)
      }
    } else {
      if (exists(name, env, inherits = FALSE)) {
          return(TRUE)
      }
      else {
          return(FALSE)
      }
    }
}
```

Test function

``` r
exists("c") # Expects TRUE
```

    ## [1] TRUE

``` r
exists("sum") # Expects TRUE
```

    ## [1] TRUE

``` r
exists("b") # Expects FALSE
```

    ## [1] FALSE

``` r
exists_new("c") # Expects TRUE
```

    ## [1] TRUE

``` r
exists_new("sum") # Expects FALSE
```

    ## [1] FALSE

``` r
exists_new("sum", inherits = TRUE) # Expects TRUE
```

    ## [1] TRUE

``` r
exists("b", inherits = TRUE) # Expects FALSE
```

    ## [1] FALSE

Function environments
---------------------

Notes:

``` r
e$g <- function() 1 # The function is created in the global environment
environment(e$g) # Therefore, the enclosing environement is still global.
```

    ## <environment: R_GlobalEnv>

``` r
where("g", env = e) # However, the binding environment is e.
```

    ## <environment: 0x000000001c5c6e60>

A function is created (enclosed) in a namespace (the imports) but bound in a package (the exports). More reference about namespace: <http://r-pkgs.had.co.nz/namespace.html>

``` r
environment(sd)
```

    ## <environment: namespace:stats>

``` r
where("sd")
```

    ## <environment: package:stats>
    ## attr(,"name")
    ## [1] "package:stats"
    ## attr(,"path")
    ## [1] "C:/Program Files/R/R-3.5.0/library/stats"

``` r
h <- function() {
  x <- 10
  function() {
    x
  }
}
i <- h()
x <- 20
i()
```

    ## [1] 10

#### 1. List the four environments associated with a function. What does each one do? Why is the distinction between enclosing and binding environments particularly important?

1.  Enclosing environments: The enclosing environment is the environment where the function was created.
2.  Binding environments: The binding environments of a function are all the environments which have a binding to it.
3.  Execution environments: Each time a function is called, a new environment is created to host execution. Once the function has completed, this environment is thrown away.
4.  Calling environments: The environment where the function is called.

The distinction between the binding environment and the enclosing environment is important for package namespaces. Package namespaces keep packages independent.

#### 2. Draw a diagram that shows the enclosing environments of this function:

``` r
f1 <- function(x1) {
  f2 <- function(x2) {
    f3 <- function(x3) {
      x1 + x2 + x3
    }
    f3(3)
  }
  f2(2)
}
f1(1)
```

    ## [1] 6

#### 3. Expand your previous diagram to show function bindings.

#### 4. Expand it again to show the execution and calling environments.

The below graph contains answers for exercise 2 to 4

<img src="C:/Users/byron/Documents/GitHub/advanced-R-exercises/FunctionEnvironmentsEx2.png" width="250px" />

#### 5. Write an enhanced version of str() that provides more information about functions. Show where the function was found and what environment it was defined in.

Original str function

``` r
str(sd)
```

    ## function (x, na.rm = FALSE)

Create function

``` r
# Original str function
str_new <- function(obj) {
  str(obj)
  def <- environment(obj)
  fd <- where(as.character(substitute(obj)))
  list(defined = def, found = fd)
}
```

Test function

``` r
str_new(mean)
```

    ## function (x, ...)

    ## $defined
    ## <environment: namespace:base>
    ## 
    ## $found
    ## <environment: base>

Binding names to values
-----------------------

#### 1. What does this function do? How does it differ from &lt;&lt;- and why might you prefer it?

``` r
rebind <- function(name, value, env = parent.frame()) {
  if (identical(env, emptyenv())) {
    stop("Can't find ", name, call. = FALSE)
  } else if (exists(name, envir = env, inherits = FALSE)) {
    assign(name, value, envir = env)
  } else {
    rebind(name, value, parent.env(env))
  }
}
rebind("a", 10)
  # Error: Can't find a
a <- 5
rebind("a", 10)
a
  # [1] 10
```

Unlike &lt;&lt;-, rebind function would not create a new object if the object doesn't exist. The function would avoid global variables introducing non-obvious dependencies between functions and thus more desirable then &lt;&lt;-.

#### 2. Create a version of assign() that will only bind new names, never re-bind old names. Some programming languages only do this, and are known as single assignment languages.

Create function

``` r
assign_new <- function(x, value, env = parent.frame()){
  if (exists(x, where = 1, envir = env, inherits = FALSE)){
    return(paste(x, "already exists."))
  } else {
    assign(x, value, envir = env)
  }
}
```

Test function

``` r
a <- 1
assign_new("a", 3)
```

    ## [1] "a already exists."

``` r
a
```

    ## [1] 1

``` r
assign_new("d", 2)
d
```

    ## [1] 2

#### 3. Write an assignment function that can do active, delayed, and locked bindings. What might you call it? What arguments should it take? Can you guess which sort of assignment it should do based on the input?

Create a function that assigns value with different binding. However, the object of delayed value has a constant name "dlv"

``` r
assignSpecial <- function(x, value, binding = "locked", env = parent.frame()){
  if (binding == "locked") { 
    assign(x, value, envir = env)
  } else if (binding == "active") {
    makeActiveBinding(x, value, env = env)
  } else if (binding == "delayed") {
    delayedAssign(x, dlv, eval.env = env, assign.env = env)
    return("Assign the delayed value to variable dlv.")
  }
}
```

Test function

``` r
assignSpecial("a", 1)
a
```

    ## [1] 1

``` r
f <- function(){
  runif(1)
}
assignSpecial("b", f, binding = "active")
b
```

    ## [1] 0.1813138

``` r
b
```

    ## [1] 0.7893826

``` r
assignSpecial("c", binding = "delayed")
```

    ## [1] "Assign the delayed value to variable dlv."

``` r
dlv = 123
c
```

    ## [1] 123

To guess the sort of assignment based on the input. Some simple logics are applied in another new assign function, assignSpecialAuto.

-   If the value is missing, use delayed assignment.
-   If the value is given and is a function, use active assignment
-   For the rest cases, use locked assignment.

Create function

``` r
assignSpecialAuto <- function(x, value, env = parent.frame()){
  if (missing(value)) {
    delayedAssign(x, dv, eval.env = env, assign.env = env)
    return("Assign the delayed value to variable dv.")
  } else if (is.function(value)) {
    makeActiveBinding(x, value, env = env)
  } else {
    assign(x, value, envir = env)
  }
}
```

Test function

``` r
# test new function
assignSpecialAuto("a1", 5)
a
```

    ## [1] 1

``` r
assignSpecialAuto("b1", f)
b1
```

    ## [1] 0.1073435

``` r
b1
```

    ## [1] 0.1534249

``` r
assignSpecialAuto("c1")
```

    ## [1] "Assign the delayed value to variable dv."

``` r
dv = 135
c1
```

    ## [1] 135
