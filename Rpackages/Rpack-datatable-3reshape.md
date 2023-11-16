R Package (self-learning): `data.table` - Reshape
================
2023-11-15

Tutorial website:
<https://rdatatable.gitlab.io/data.table/articles/datatable-reshape.html>

``` r
library(data.table)
```

## Introduction

The `data.table` package developes (?) two functions to reshape the data
tables:

- `melt()`: *wide to long*;  
- `dcast()`: *long to wide*.

The implementations are specifically designed with large in-memory data
(e.g. 10Gb) in mind.

## 1. Default functionality

### a) `melt`ing `data.table`s (wide to long)

``` r
s1 <- "family_id age_mother dob_child1 dob_child2 dob_child3
1         30 1998-11-26 2000-01-29         NA
2         27 1996-06-22         NA         NA
3         26 2002-07-11 2004-04-05 2007-09-02
4         32 2004-10-10 2009-08-27 2012-07-21
5         29 2000-12-05 2005-02-28         NA"
DT <- fread(s1)
DT
```

    ##    family_id age_mother dob_child1 dob_child2 dob_child3
    ##        <int>      <int>     <IDat>     <IDat>     <IDat>
    ## 1:         1         30 1998-11-26 2000-01-29       <NA>
    ## 2:         2         27 1996-06-22       <NA>       <NA>
    ## 3:         3         26 2002-07-11 2004-04-05 2007-09-02
    ## 4:         4         32 2004-10-10 2009-08-27 2012-07-21
    ## 5:         5         29 2000-12-05 2005-02-28       <NA>

``` r
str(DT)
```

    ## Classes 'data.table' and 'data.frame':   5 obs. of  5 variables:
    ##  $ family_id : int  1 2 3 4 5
    ##  $ age_mother: int  30 27 26 32 29
    ##  $ dob_child1: IDate, format: "1998-11-26" "1996-06-22" ...
    ##  $ dob_child2: IDate, format: "2000-01-29" NA ...
    ##  $ dob_child3: IDate, format: NA NA ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

#### - Convert `DT` to *long* form where each `dob` is a separate observation.

We can specify the following two arguments in the `melt()` function:

- `id.vars`: specify the set of columns we would like to keep them
  identical.

- `measure.vars`: specify the set of columns we would like to collapse
  (or combine) together;

  - We can also specify *column indices* instead of *names*.

- By default, when one of `id.vars` or `measure.vars` is missing, the
  rest of the columns are automatically assigned to the missing
  argument. Shown in the [next example](#1a2).

For example:

``` r
DT.m1 <- melt(DT, id.vars = c("family_id", "age_mother"),
                  measure.vars = c("dob_child1", "dob_child2", "dob_child3"))

DT.m1
```

    ##     family_id age_mother   variable      value
    ##         <int>      <int>     <fctr>     <IDat>
    ##  1:         1         30 dob_child1 1998-11-26
    ##  2:         2         27 dob_child1 1996-06-22
    ##  3:         3         26 dob_child1 2002-07-11
    ##  4:         4         32 dob_child1 2004-10-10
    ##  5:         5         29 dob_child1 2000-12-05
    ##  6:         1         30 dob_child2 2000-01-29
    ##  7:         2         27 dob_child2       <NA>
    ##  8:         3         26 dob_child2 2004-04-05
    ##  9:         4         32 dob_child2 2009-08-27
    ## 10:         5         29 dob_child2 2005-02-28
    ## 11:         1         30 dob_child3       <NA>
    ## 12:         2         27 dob_child3       <NA>
    ## 13:         3         26 dob_child3 2007-09-02
    ## 14:         4         32 dob_child3 2012-07-21
    ## 15:         5         29 dob_child3       <NA>

``` r
str(DT.m1)
```

    ## Classes 'data.table' and 'data.frame':   15 obs. of  4 variables:
    ##  $ family_id : int  1 2 3 4 5 1 2 3 4 5 ...
    ##  $ age_mother: int  30 27 26 32 29 30 27 26 32 29 ...
    ##  $ variable  : Factor w/ 3 levels "dob_child1","dob_child2",..: 1 1 1 1 1 2 2 2 2 2 ...
    ##  $ value     : IDate, format: "1998-11-26" "1996-06-22" ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

- By default, the molten columns are automatically named `variable` and
  `value`.

- By default, `variable` column is of type `factor`.

  - Set argument `variable.factor` to `FALSE` if you’d like to return a
    `character` vector instead.

- `melt` preserves column attributes in result.

#### - Name the `variable` and `value` to `child` and `dob` respectively.

``` r
DT.m1 <- melt(DT, measure.vars = c("dob_child1", "dob_child2", "dob_child3"), 
                variable.name = "child", value.name = "dob")
DT.m1 
```

    ##     family_id age_mother      child        dob
    ##         <int>      <int>     <fctr>     <IDat>
    ##  1:         1         30 dob_child1 1998-11-26
    ##  2:         2         27 dob_child1 1996-06-22
    ##  3:         3         26 dob_child1 2002-07-11
    ##  4:         4         32 dob_child1 2004-10-10
    ##  5:         5         29 dob_child1 2000-12-05
    ##  6:         1         30 dob_child2 2000-01-29
    ##  7:         2         27 dob_child2       <NA>
    ##  8:         3         26 dob_child2 2004-04-05
    ##  9:         4         32 dob_child2 2009-08-27
    ## 10:         5         29 dob_child2 2005-02-28
    ## 11:         1         30 dob_child3       <NA>
    ## 12:         2         27 dob_child3       <NA>
    ## 13:         3         26 dob_child3 2007-09-02
    ## 14:         4         32 dob_child3 2012-07-21
    ## 15:         5         29 dob_child3       <NA>

- By default, when one of `id.vars` or `measure.vars` is missing, the
  rest of the columns are automatically assigned to the missing
  argument.

- When neither `id.vars` nor `measure.vars` are specified, all
  *non-*`numeric`, `integer`, `logical` columns will be assigned to
  `id.vars`.

### b) `dcast`ing `data.table`s (long to wide)

#### - Get back to the original data table `DT` from `DT.m1`

We use `dcast` function to accomplish it.

``` r
dcast(DT.m1, family_id + age_mother ~ child, value.var = "dob")
```

    ## Key: <family_id, age_mother>
    ##    family_id age_mother dob_child1 dob_child2 dob_child3
    ##        <int>      <int>     <IDat>     <IDat>     <IDat>
    ## 1:         1         30 1998-11-26 2000-01-29       <NA>
    ## 2:         2         27 1996-06-22       <NA>       <NA>
    ## 3:         3         26 2002-07-11 2004-04-05 2007-09-02
    ## 4:         4         32 2004-10-10 2009-08-27 2012-07-21
    ## 5:         5         29 2000-12-05 2005-02-28       <NA>

- `dcast` uses *formula* interface: `id variables ~ measure variables`

  - the variables on the *LHS* of formula represents the *id* vars;  
  - those on the *RHS* represents the *measure* vars.

- `value.var` denotes the column to be filled in while casting to wide
  format.

- `dcast()` also tries to preserve attributes in result wherever
  posible.

#### - Starting from `DT.m1`, how can we get the number of children in each family?

Pass a function to aggregate by in `dcast()` with the argument
`fun.aggregate`. This is particular essential when the formula provided
does not identify single observation for each cell.

``` r
dcast(DT.m1, family_id ~., 
      fun.agg = function(x) sum(!is.na(x)), 
      value.var = "dob")
```

    ## Key: <family_id>
    ##    family_id     .
    ##        <int> <int>
    ## 1:         1     2
    ## 2:         2     1
    ## 3:         3     3
    ## 4:         4     3
    ## 5:         5     2

## 2. Limitations in current `melt/dcast` approaches

Here is another example:

``` r
s2 <- "family_id age_mother dob_child1 dob_child2 dob_child3 gender_child1 gender_child2 gender_child3
1         30 1998-11-26 2000-01-29         NA             1             2            NA
2         27 1996-06-22         NA         NA             2            NA            NA
3         26 2002-07-11 2004-04-05 2007-09-02             2             2             1
4         32 2004-10-10 2009-08-27 2012-07-21             1             1             1
5         29 2000-12-05 2005-02-28         NA             2             1            NA"
DT <- fread(s2)
DT
```

    ##    family_id age_mother dob_child1 dob_child2 dob_child3 gender_child1
    ##        <int>      <int>     <IDat>     <IDat>     <IDat>         <int>
    ## 1:         1         30 1998-11-26 2000-01-29       <NA>             1
    ## 2:         2         27 1996-06-22       <NA>       <NA>             2
    ## 3:         3         26 2002-07-11 2004-04-05 2007-09-02             2
    ## 4:         4         32 2004-10-10 2009-08-27 2012-07-21             1
    ## 5:         5         29 2000-12-05 2005-02-28       <NA>             2
    ##    gender_child2 gender_child3
    ##            <int>         <int>
    ## 1:             2            NA
    ## 2:            NA            NA
    ## 3:             2             1
    ## 4:             1             1
    ## 5:             1            NA

Suppose we’d likd to combine (`melt`) all the `dob` columns together,
and `gender` columns together. We may do something like this:

``` r
# 1. & 2.
DT.m1 = melt(DT, id.var = c("family_id", "age_mother"))  
# 3. 
DT.m1[, c("variable", "child") := tstrsplit(variable, "_", fixed = TRUE)]
# 4. 
DT.c1 = dcast(DT.m1, family_id + age_mother + child ~ variable, value.var = "value")

DT.c1
```

    ## Key: <family_id, age_mother, child>
    ##     family_id age_mother  child        dob     gender
    ##         <int>      <int> <char>     <IDat>     <IDat>
    ##  1:         1         30 child1 1998-11-26 1970-01-02
    ##  2:         1         30 child2 2000-01-29 1970-01-03
    ##  3:         1         30 child3       <NA>       <NA>
    ##  4:         2         27 child1 1996-06-22 1970-01-03
    ##  5:         2         27 child2       <NA>       <NA>
    ##  6:         2         27 child3       <NA>       <NA>
    ##  7:         3         26 child1 2002-07-11 1970-01-03
    ##  8:         3         26 child2 2004-04-05 1970-01-03
    ##  9:         3         26 child3 2007-09-02 1970-01-02
    ## 10:         4         32 child1 2004-10-10 1970-01-02
    ## 11:         4         32 child2 2009-08-27 1970-01-02
    ## 12:         4         32 child3 2012-07-21 1970-01-02
    ## 13:         5         29 child1 2000-12-05 1970-01-03
    ## 14:         5         29 child2 2005-02-28 1970-01-02
    ## 15:         5         29 child3       <NA>       <NA>

``` r
str(DT.c1)
```

    ## Classes 'data.table' and 'data.frame':   15 obs. of  5 variables:
    ##  $ family_id : int  1 1 1 2 2 2 3 3 3 4 ...
    ##  $ age_mother: int  30 30 30 27 27 27 26 26 26 32 ...
    ##  $ child     : chr  "child1" "child2" "child3" "child1" ...
    ##  $ dob       : IDate, format: "1998-11-26" "2000-01-29" ...
    ##  $ gender    : IDate, format: "1970-01-02" "1970-01-03" ...
    ##  - attr(*, ".internal.selfref")=<externalptr> 
    ##  - attr(*, "sorted")= chr [1:3] "family_id" "age_mother" "child"

**Issues:**

1.  We combine both `dob` and `gender` columns together, and then split
    them again, which is quite “roundabout” and inefficient.

2.  The columns to `melt` may be of different types, as in this case
    (`character` and `integer`). By `melt`ing them all together, the
    columns will be coerced in result: in this case, `gender` has been
    converted to `character` type.

3.  We are generating an additional column by splitting the `variable`
    column into two columns, whose purpose is quite cryptic. We do it
    because we need it for *casting* in the next step.

4.  Finally, we cast the data set. But it requires computing the order
    of the variables in formula, which is much more computationally
    involved operation than `melt`.

Well, try `stats::reshape()`!

## 3. Enhanced (new) functionality

### a) Enhanced `melt()`

#### - `melt` multiple columns simultaneously

We may pass a list of columns to `measure.vars`, where each element of
the list contains the columns that should be combined together.

``` r
colA = paste0("dob_child", 1:3)
colB = paste0("gender_child", 1:3)
DT.m2 = melt(DT, measure = list(colA, colB), 
             value.name = c("dob", "gender"))
DT.m2
```

    ##     family_id age_mother variable        dob gender
    ##         <int>      <int>   <fctr>     <IDat>  <int>
    ##  1:         1         30        1 1998-11-26      1
    ##  2:         2         27        1 1996-06-22      2
    ##  3:         3         26        1 2002-07-11      2
    ##  4:         4         32        1 2004-10-10      1
    ##  5:         5         29        1 2000-12-05      2
    ##  6:         1         30        2 2000-01-29      2
    ##  7:         2         27        2       <NA>     NA
    ##  8:         3         26        2 2004-04-05      2
    ##  9:         4         32        2 2009-08-27      1
    ## 10:         5         29        2 2005-02-28      1
    ## 11:         1         30        3       <NA>     NA
    ## 12:         2         27        3       <NA>     NA
    ## 13:         3         26        3 2007-09-02      1
    ## 14:         4         32        3 2012-07-21      1
    ## 15:         5         29        3       <NA>     NA

``` r
str(DT.m2)
```

    ## Classes 'data.table' and 'data.frame':   15 obs. of  5 variables:
    ##  $ family_id : int  1 2 3 4 5 1 2 3 4 5 ...
    ##  $ age_mother: int  30 27 26 32 29 30 27 26 32 29 ...
    ##  $ variable  : Factor w/ 3 levels "1","2","3": 1 1 1 1 1 2 2 2 2 2 ...
    ##  $ dob       : IDate, format: "1998-11-26" "1996-06-22" ...
    ##  $ gender    : int  1 2 2 1 2 2 NA 2 1 1 ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

- We can remove the `variable` column if necessary.

#### - Using `patterns()`

The columns we’d like to melt may be distinguished by a common pattern.
We can use the function `patterns()` to provide regular expressions for
the columns to be combined together.

``` r
DT.m2 = melt(DT, measure = patterns("^dob", "^gender"), 
             value.name = c("dob", "gender"))
print(DT.m2, class = TRUE)
```

    ##     family_id age_mother variable        dob gender
    ##         <int>      <int>   <fctr>     <IDat>  <int>
    ##  1:         1         30        1 1998-11-26      1
    ##  2:         2         27        1 1996-06-22      2
    ##  3:         3         26        1 2002-07-11      2
    ##  4:         4         32        1 2004-10-10      1
    ##  5:         5         29        1 2000-12-05      2
    ##  6:         1         30        2 2000-01-29      2
    ##  7:         2         27        2       <NA>     NA
    ##  8:         3         26        2 2004-04-05      2
    ##  9:         4         32        2 2009-08-27      1
    ## 10:         5         29        2 2005-02-28      1
    ## 11:         1         30        3       <NA>     NA
    ## 12:         2         27        3       <NA>     NA
    ## 13:         3         26        3 2007-09-02      1
    ## 14:         4         32        3 2012-07-21      1
    ## 15:         5         29        3       <NA>     NA

#### - Using `measure()` to specify `measure.vars` via separator or pattern

If the input columns to melt have regular names, we can use `measure`,
which allows specifying the columns to melt via a separator or a regex.

For example:

``` r
 (two.iris <- data.table(datasets::iris)[c(1, 150)])
```

    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    ##           <num>       <num>        <num>       <num>    <fctr>
    ## 1:          5.1         3.5          1.4         0.2    setosa
    ## 2:          5.9         3.0          5.1         1.8 virginica

The iris data has four numeric columns with a regular structure: “flower
part + . + measurement dimension”. To specify that we want to melt those
four columns, we can use `measure` with `sep = "."`, which means to use
`strsplit` on all column names; the columns which result in the maximum
number of groups after splitting will be used as `measure.var`:

``` r
melt(two.iris, measure.vars = measure(part, dim, sep = "."))
```

    ##      Species   part    dim value
    ##       <fctr> <char> <char> <num>
    ## 1:    setosa  Sepal Length   5.1
    ## 2: virginica  Sepal Length   5.9
    ## 3:    setosa  Sepal  Width   3.5
    ## 4: virginica  Sepal  Width   3.0
    ## 5:    setosa  Petal Length   1.4
    ## 6: virginica  Petal Length   5.1
    ## 7:    setosa  Petal  Width   0.2
    ## 8: virginica  Petal  Width   1.8

- The first two arguments to `measure()` in the code above (`part` and
  `dim`) are used to name the output columns;
  - the number of arguments must equal the max number of groups after
    splitting with `sep`.

If we want two value columns, one for each part, we can use the special
`value.name` keyword, which means to **output a value column for each
unique name found in that group**:

``` r
melt(two.iris, measure.vars = measure(value.name, dim, sep = "."))
```

    ##      Species    dim Sepal Petal
    ##       <fctr> <char> <num> <num>
    ## 1:    setosa Length   5.1   1.4
    ## 2: virginica Length   5.9   5.1
    ## 3:    setosa  Width   3.5   0.2
    ## 4: virginica  Width   3.0   1.8

Similarly,

``` r
melt(two.iris, measure.vars = measure(part, value.name, sep = "."))
```

    ##      Species   part Length Width
    ##       <fctr> <char>  <num> <num>
    ## 1:    setosa  Sepal    5.1   3.5
    ## 2: virginica  Sepal    5.9   3.0
    ## 3:    setosa  Petal    1.4   0.2
    ## 4: virginica  Petal    5.1   1.8

Let’s go back to the example of the data with families and children.

``` r
DT
```

    ##    family_id age_mother dob_child1 dob_child2 dob_child3 gender_child1
    ##        <int>      <int>     <IDat>     <IDat>     <IDat>         <int>
    ## 1:         1         30 1998-11-26 2000-01-29       <NA>             1
    ## 2:         2         27 1996-06-22       <NA>       <NA>             2
    ## 3:         3         26 2002-07-11 2004-04-05 2007-09-02             2
    ## 4:         4         32 2004-10-10 2009-08-27 2012-07-21             1
    ## 5:         5         29 2000-12-05 2005-02-28       <NA>             2
    ##    gender_child2 gender_child3
    ##            <int>         <int>
    ## 1:             2            NA
    ## 2:            NA            NA
    ## 3:             2             1
    ## 4:             1             1
    ## 5:             1            NA

We would like to collapse all `dob` columns together, and `gender`
columns together. Besides, we would like to convert the `child` string
values into integers:

``` r
DT.m3 = melt(DT, measure = measure(value.name, child = as.integer, sep = "_child"))
print(DT.m3)
```

    ##     family_id age_mother child        dob gender
    ##         <int>      <int> <int>     <IDat>  <int>
    ##  1:         1         30     1 1998-11-26      1
    ##  2:         2         27     1 1996-06-22      2
    ##  3:         3         26     1 2002-07-11      2
    ##  4:         4         32     1 2004-10-10      1
    ##  5:         5         29     1 2000-12-05      2
    ##  6:         1         30     2 2000-01-29      2
    ##  7:         2         27     2       <NA>     NA
    ##  8:         3         26     2 2004-04-05      2
    ##  9:         4         32     2 2009-08-27      1
    ## 10:         5         29     2 2005-02-28      1
    ## 11:         1         30     3       <NA>     NA
    ## 12:         2         27     3       <NA>     NA
    ## 13:         3         26     3 2007-09-02      1
    ## 14:         4         32     3 2012-07-21      1
    ## 15:         5         29     3       <NA>     NA

- Here, columns containg the strings `_child` are melted:  
  `dob_child1`, `dob_child2`, `dob_child3`, `gender_child1`,
  `gender_child2`, and `gender_child3`.

- Specified by `value.name`, strings before `_child` are used to name
  the new columns; we have two new columns named `dob` and `gender`.

- `child=as.integer` means the second group will result in an output
  column named `child` with values defined by plugging the character
  strings from that group into `as.integer`.

Finally consider the following example where we need to define the
groups using a regular expression rather than a separator.

``` r
(who <- data.table(id = 1, new_sp_m5564 = 2, newrel_f65 = 3))
```

    ##       id new_sp_m5564 newrel_f65
    ##    <num>        <num>      <num>
    ## 1:     1            2          3

``` r
melt(who, measure.vars = measure(
     diagnosis, gender, ages, pattern = "new_?(.*)_(.)(.*)"))
```

    ##       id diagnosis gender   ages value
    ##    <num>    <char> <char> <char> <num>
    ## 1:     1        sp      m   5564     2
    ## 2:     1       rel      f     65     3

> To learn more about regular expression, here is a [quick
> reference](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference)

When using the `pattern` argument, it must be a Perl-compatible regular
expression containing the same number of capture groups (parenthesized
sub-expressions) as the number other arguments (group numbers).

A more complex example:

``` r
print(
    melt(who, measure.vars = measure(
        diagnosis, gender, ages, 
        ymin = as.numeric, 
        ymax = function(y) ifelse(y == "", Inf, as.numeric(y)), 
        pattern = "new_?(.*)_(.)(([0-9]{2})([0-9]{0,2}))"
    )),
    class = TRUE
)
```

    ##       id diagnosis gender   ages  ymin  ymax value
    ##    <num>    <char> <char> <char> <num> <num> <num>
    ## 1:     1        sp      m   5564    55    64     2
    ## 2:     1       rel      f     65    65   Inf     3

### b) Enhanced `dcast`

Given `DT.m2` as shown above, how can we get back to the same format as
the original data?

``` r
DT.m2
```

    ##     family_id age_mother variable        dob gender
    ##         <int>      <int>   <fctr>     <IDat>  <int>
    ##  1:         1         30        1 1998-11-26      1
    ##  2:         2         27        1 1996-06-22      2
    ##  3:         3         26        1 2002-07-11      2
    ##  4:         4         32        1 2004-10-10      1
    ##  5:         5         29        1 2000-12-05      2
    ##  6:         1         30        2 2000-01-29      2
    ##  7:         2         27        2       <NA>     NA
    ##  8:         3         26        2 2004-04-05      2
    ##  9:         4         32        2 2009-08-27      1
    ## 10:         5         29        2 2005-02-28      1
    ## 11:         1         30        3       <NA>     NA
    ## 12:         2         27        3       <NA>     NA
    ## 13:         3         26        3 2007-09-02      1
    ## 14:         4         32        3 2012-07-21      1
    ## 15:         5         29        3       <NA>     NA

#### - Casting multiple `value.var`s simultaneously

We can provide multiple `value.var` columns to `dcast` for `data.table`s
directly.

``` r
## new 'cast' functionality - multiple value.vars 
DT.c2 = dcast(DT.m2, family_id + age_mother ~ variable, 
                     value.var = c("dob", "gender"))
DT.c2
```

    ## Key: <family_id, age_mother>
    ##    family_id age_mother      dob_1      dob_2      dob_3 gender_1 gender_2
    ##        <int>      <int>     <IDat>     <IDat>     <IDat>    <int>    <int>
    ## 1:         1         30 1998-11-26 2000-01-29       <NA>        1        2
    ## 2:         2         27 1996-06-22       <NA>       <NA>        2       NA
    ## 3:         3         26 2002-07-11 2004-04-05 2007-09-02        2        2
    ## 4:         4         32 2004-10-10 2009-08-27 2012-07-21        1        1
    ## 5:         5         29 2000-12-05 2005-02-28       <NA>        2        1
    ##    gender_3
    ##       <int>
    ## 1:       NA
    ## 2:       NA
    ## 3:        1
    ## 4:        1
    ## 5:       NA

#### Multiple functions to `fun.aggregate`.

Check examples in `?dcast` that illustrates this functionality.
