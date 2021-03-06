OO Field Guide
================
Byron Tang
May 26, 2018

S3
--

Note:

-   S3 implements a style of OO programming called generic-function OO
-   To determine if a function is an S3 generic, you can inspect its source code for a call to UseMethod()
-   You can see all the methods that belong to a generic with methods()
-   The underlying codes for the outputs (for unknown classes) of generic functions could be inspected in generic.defualt()
-   Some S3 generics, like \[, sum(), and cbind(), don't call UseMethod() because they are implemented in C. Instead, they call the C functions DispatchGroup() or DispatchOrEval(). Functions that do method dispatch in C code are called internal generics and are documented in ?"internal generic"

Additional Materials:

-   <https://www.cyclismo.org/tutorial/R/s3Classes.html>

#### 1. Read the source code for t() and t.test() and confirm that t.test() is an S3 generic and not an S3 method. What happens if you create an object with class test and call t() with it?

In the source code of t.test, there's a line of code says UseMethod("t.test"), which means t.test() is an S3 generic and not an S3 method of t().

In order to let generics t() to call a method for an object with class test, the naming of the method should be t.test, which is used for an existing function. As the result, after another t.test is created, when calling original t.test, it is neccessary to specify stats:: in order to call the function correctly.

``` r
t
```

    ## function (x) 
    ## UseMethod("t")
    ## <bytecode: 0x000000001c2285a8>
    ## <environment: namespace:base>

``` r
stats::t.test
```

    ## function (x, ...) 
    ## UseMethod("t.test")
    ## <bytecode: 0x0000000017c91ee8>
    ## <environment: namespace:stats>

``` r
t.test <- function(x) "A method"
# Use method t.test\n
t(structure(matrix(1:30, 5, 6), class = "test"))
```

    ## [1] "A method"

``` r
# Function t.test is overwritten\n
tryCatch(t.test(1:10, y = c(7:20)), 
         error = function(e) e) # throws an error
```

    ## <simpleError in t.test(1:10, y = c(7:20)): unused argument (y = c(7:20))>

``` r
# Specify stats package to call function t.test\n
stats::t.test(1:10, y = c(7:20)) # specify stats package is required.
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  1:10 and c(7:20)
    ## t = -5.4349, df = 21.982, p-value = 1.855e-05
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -11.052802  -4.947198
    ## sample estimates:
    ## mean of x mean of y 
    ##       5.5      13.5

#### 2. What classes have a method for the Math group generic in base R? Read the source code. How do the methods work?

data.frame, Date, difftime, factor, and POSIXt

``` r
methods(Math)
```

    ## [1] Math,nonStructure-method Math,structure-method   
    ## [3] Math.data.frame          Math.Date               
    ## [5] Math.difftime            Math.factor             
    ## [7] Math.POSIXt             
    ## see '?methods' for accessing help and source code

Math() calls standardGeneric(). standardGeneric dispatches the method defined for a generic function, using the actual arguments in the frame from which it is called.

Group generic Math

``` r
Math
```

    ## groupGenericFunction for "Math" defined from package "base"
    ## 
    ## function (x) 
    ## standardGeneric("Math")
    ## <bytecode: 0x0000000019481838>
    ## <environment: 0x00000000177a2118>
    ## Methods may be defined for arguments: x
    ## Use  showMethods("Math")  for currently available ones.

#### 3. R has two classes for representing date time data, POSIXct and POSIXlt, which both inherit from POSIXt. Which generics have different behaviours for the two classes? Which generics share the same behaviour?

``` r
POSIXct_generics <- gsub(".POSIXct", "", as.vector(methods(class = "POSIXct")))
POSIXlt_generics <- gsub(".POSIXlt", "", as.vector(methods(class = "POSIXlt")))

# Different behaviours for the two classes
union(setdiff(POSIXct_generics, POSIXlt_generics), 
      setdiff(POSIXlt_generics, POSIXct_generics))
```

    ##  [1] "as.POSIXlt" "split"      "anyNA"      "as.double"  "as.matrix" 
    ##  [6] "as.POSIXct" "duplicated" "is.na"      "length"     "names"     
    ## [11] "names<-"    "sort"       "unique"

``` r
#Same behaviours for the two classes
intersect(POSIXct_generics, POSIXlt_generics)
```

    ##  [1] "["                           "[["                         
    ##  [3] "[<-"                         "as.data.frame"              
    ##  [5] "as.Date"                     "as.list"                    
    ##  [7] "c"                           "coerce,oldClass,S3-method"  
    ##  [9] "format"                      "initialize,oldClass-method" 
    ## [11] "length<-"                    "mean"                       
    ## [13] "print"                       "rep"                        
    ## [15] "show,oldClass-method"        "slotsFromS3,oldClass-method"
    ## [17] "summary"                     "Summary"                    
    ## [19] "weighted.mean"               "xtfrm"

#### 4. Which base generic has the greatest number of defined methods?

``` r
# get all the base generics
objs <- mget(ls("package:base"), inherits = TRUE)
is_generic <- grepl("generic", sapply(objs, ftype))
# is_generic <- grepl("UseMethod", objs) # incomplete list, only includes S3 generic
funs_generic <- objs[is_generic]
S3_methods_list <- lapply(names(funs_generic), methods)

# S3 generic with most methods
most_S3_methods <- 
  funs_generic[sapply(S3_methods_list, length) == 
                 max(sapply(S3_methods_list, length))]
names(most_S3_methods)
```

    ## [1] "summary"

``` r
# Number of methods
length(methods(names(most_S3_methods)))
```

    ## [1] 36

#### 5. UseMethod() calls methods in a special way. Predict what the following code will return, then run it and read the help for UseMethod() to figure out what's going on. Write down the rules in the simplest form possible.

``` r
y <- 1
g <- function(x) {
  y <- 2
  UseMethod("g")
}
g.numeric <- function(x) y
g(10)

h <- function(x) {
  x <- 10
  UseMethod("h")
}
h.character <- function(x) paste("char", x)
h.numeric <- function(x) paste("num", x)

h("a")
```

The code creates two generic funtions, g() and h(), and methods for each generic. One method of g() is created for numeric objects, and two medhos of f() are created for character and numeric objects.

Run the codes

``` r
y <- 1
g <- function(x) {
  y <- 2
  UseMethod("g")
}
g.numeric <- function(x) y
g(10)
```

    ## [1] 2

``` r
h <- function(x) {
  x <- 10
  UseMethod("h")
}
h.character <- function(x) paste("char", x)
h.numeric <- function(x) paste("num", x)

h("a")
```

    ## [1] "char a"

``` r
h(2)
```

    ## [1] "num 2"

``` r
tryCatch(h(TRUE), error = function(e) e)
```

    ## <simpleError in UseMethod("h"): no applicable method for 'h' applied to an object of class "logical">

#### 6. Internal generics don't dispatch on the implicit class of base types. Carefully read ?"internal generic" to determine why the length of f and g is different in the example below. What function helps distinguish between the behaviour of f and g?

``` r
f <- function() 1
g <- function() 2
class(g) <- "function"

class(f)
class(g)

length.function <- function(x) "function"
length(f)
length(g)
```

The class of f is implicit while the class of g is explicitly assigned. Therefore, althought both f and g have the class of funciton, passing f to length(), a primitive generic function, would not dispatch a method, but passing g to length() would dispatch length.function()

Run the codes

``` r
f <- function() 1
g <- function() 2
class(g) <- "function"

class(f)
```

    ## [1] "function"

``` r
class(g)
```

    ## [1] "function"

``` r
length.function <- function(x) "function"
length(f)
```

    ## [1] 1

``` r
length(g)
```

    ## [1] "function"

``` r
ftype(length)
```

    ## [1] "primitive" "generic"

S4
--

Notes:

You can get a list of all S4 generics with getGenerics(), and a list of all S4 classes with getClasses(). This list includes shim classes for S3 classes and base types. You can list all S4 methods with showMethods(), optionally restricting selection either by generic or by class (or both). It's also a good idea to supply where = search() to restrict the search to methods available in the global environment.

Additional Materials:

-   <https://www.cyclismo.org/tutorial/R/s4Classes.html>
-   <https://www.coursera.org/learn/bioconductor/lecture/N1xBf/r-s4-classes>

#### 1. Which S4 generic has the most methods defined for it? Which S4 class has the most methods associated with it?

All S4 related code is stored in the methods package.

``` r
methods_objs <- mget(ls("package:methods"), inherits = TRUE)
is_S4 <- sapply(methods_objs, isS4)
S4_generics <- methods_objs[is_S4]
S4_methods_list <- lapply(names(S4_generics), .S4methods)

# S4 generic with most methods
most_S4_methods <- 
  S4_generics[sapply(S4_methods_list, length) == 
                max(sapply(S4_methods_list, length))]
names(most_S4_methods)
```

    ## [1] "coerce" "show"

``` r
# Number of methods
length(methods(names(most_S4_methods[1])))
```

    ## [1] 27

Tried hard but could not find the S4 class that has the most methods associated with it.

#### 2. What happens if you define a new S4 class that doesn't "contain" an existing class? (Hint: read about virtual classes in ?setClass.)

A virtual class would be created when the contain argument is not supplied. No actual objects can be created from the virtual classes. A virtual class may include slots to provide some common behavior without fully defining the object. Note that 'VIRTUAL' does not carry over to subclasses; a class that contains a virtual class is not itself automatically virtual.

#### 3. What happens if you pass an S4 object to an S3 generic? What happens if you pass an S3 object to an S4 generic? (Hint: read ?setOldClass for the second case.)

S4 classes can be used for any S3 method selection; when an S4 object is detected, S3 method selection uses the contents of extends(class(x)) as the equivalent of the S3 inheritance (the inheritance is cached after the first call). An existing S3 method may not behave as desired for an S4 subclass, in which case utilities such as asS3 and S3Part may be useful. If the S3 method fails on the S4 object, asS3(x) may be passed instead; if the object returned by the S3 method needs to be incorporated in the S4 object, the replacement function for S3Part may be useful, as in the method for class 'myFrame' in the examples.

S3 classes can be used for any S4 method selection, provided that the S3 classes have been registered by a call to setOldClass, with that call specifying the correct S3 inheritance pattern. A call to setOldClass creates formal classes corresponding to S3 classes, allows these to be used as slots in other classes or in a signature in setMethod, and mimics the S3 inheritance.

Reference:

-   <https://www.rdocumentation.org/packages/methods/versions/3.3.1/topics/Methods>
-   <https://www.rdocumentation.org/packages/methods/versions/3.3.1/topics/setOldClass>

RC
--

#### 1. Use a field function to prevent the account balance from being directly manipulated. (Hint: create a "hidden" .balance field, and read the help for the fields argument in setRefClass().)

As a related feature, the element in the fields= list supplied to setRefClass can be an accessor function, a function of one argument that returns the field if called with no argument or sets it to the value of the argument otherwise. Accessor functions are used internally and for inter-system interface applications, but not generally recommended as they blur the concept of fields as data within the object.

A field, say data, can be accessed generally by an expression of the form x$data for any object from the relevant class. In an internal method for this class, the field can be accessed by the name data. A field that is not locked can be set by an expression of the form x$data &lt;- value. Inside an internal method, a field can be assigned by an expression of the form x &lt;&lt;- value. Note the non-local assignment operator. The standard R interpretation of this operator works to assign it in the environment of the object. If the field has an accessor function defined, getting and setting will call that function.

Reference:

-   <https://www.rdocumentation.org/packages/methods/versions/3.5.0/topics/ReferenceClasses>

#### 2. I claimed that there aren't any RC classes in base R, but that was a bit of a simplification. Use getClasses() and find which classes extend() from envRefClass. What are the classes used for? (Hint: recall how to look up the documentation for a class.)

envRefClass: All reference classes eventually inherit from envRefClass.

refGeneratorSlot: The call to setRefClass defines the specified class and returns a 'generator function' object for that class.

localRefClass: Local reference classes are modified ReferenceClasses that isolate the objects to the local frame. Therefore, they do not propagate changes back to the calling environment. At the same time, they use the reference field semantics locally, avoiding the automatic duplication applied to standard R objects.

``` r
all_classes <- getClasses(where = .GlobalEnv, inherits = TRUE)
ext_classes <- sapply(all_classes, extends)
from_envRefClass <- grepl("envRefClass", ext_classes)
ext_classes[from_envRefClass]
```

    ## $envRefClass
    ## [1] "envRefClass"  ".environment" "refClass"     "environment" 
    ## [5] "refObject"   
    ## 
    ## $refGeneratorSlot
    ## [1] "refGeneratorSlot" "envRefClass"      ".environment"    
    ## [4] "refClass"         "environment"      "refObject"       
    ## 
    ## $localRefClass
    ## [1] "localRefClass" "envRefClass"   ".environment"  "refClass"     
    ## [5] "environment"   "refObject"
