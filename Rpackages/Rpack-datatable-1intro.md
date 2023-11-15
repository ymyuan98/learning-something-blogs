R Package (self-learning): `data.table` - Introduction
================
2023-11-14

Tutorial website:
<https://cran.r-project.org/web/packages/data.table/vignettes/datatable-intro.html>

(I copied the contents in the vignette to teach myself, thus the
following contents are basically the same as those in the vignette.)

``` r
library(data.table)
```

Installing R package `data.table` in MacOS may encounter a problem of
not detecting OpenMP support. It indicates that we are using
single-threaded mode instead of the faster multi-threaded mode. To deal
with it, read [this](https://mac.r-project.org/openmp/) and
[this](https://github.com/Rdatatable/data.table/wiki/Installation).

## Data

- Use file reader `fread` to load data directly.

Following the tutorial, we use
[NYC-flights14](https://raw.githubusercontent.com/Rdatatable/data.table/master/vignettes/flights14.csv)
obtained by [flights](https://github.com/arunsrinivasan/flights)
package.

``` r
input <- if(file.exists("flights14.csv")) {
    "flights14.csv"
} else {
    "https://raw.githubusercontent.com/Rdatatable/data.table/master/vignettes/flights14.csv"
}
flights <- fread(input)
flights
```

    ##          year month   day dep_delay arr_delay carrier origin   dest air_time
    ##         <int> <int> <int>     <int>     <int>  <char> <char> <char>    <int>
    ##      1:  2014     1     1        14        13      AA    JFK    LAX      359
    ##      2:  2014     1     1        -3        13      AA    JFK    LAX      363
    ##      3:  2014     1     1         2         9      AA    JFK    LAX      351
    ##      4:  2014     1     1        -8       -26      AA    LGA    PBI      157
    ##      5:  2014     1     1         2         1      AA    JFK    LAX      350
    ##     ---                                                                     
    ## 253312:  2014    10    31         1       -30      UA    LGA    IAH      201
    ## 253313:  2014    10    31        -5       -14      UA    EWR    IAH      189
    ## 253314:  2014    10    31        -8        16      MQ    LGA    RDU       83
    ## 253315:  2014    10    31        -4        15      MQ    LGA    DTW       75
    ## 253316:  2014    10    31        -5         1      MQ    LGA    SDF      110
    ##         distance  hour
    ##            <int> <int>
    ##      1:     2475     9
    ##      2:     2475    11
    ##      3:     2475    19
    ##      4:     1035     7
    ##      5:     2475    13
    ##     ---               
    ## 253312:     1416    14
    ## 253313:     1400     8
    ## 253314:      431    11
    ## 253315:      502    11
    ## 253316:      659     8

Aside: `fread` accepts `http` and `https` URLs directly as well as
operating system commands such as `sed` and `awk` output. See `?fread`
for examples.

## 1. Basics

`data.table` is an R package providing an enhanced version of
`data.frame`s.

### a) Creat a `data.table`

- `data.table()`: create a `data.table` from scratch; similar to
  `data.frame()` function.

``` r
DT = data.table(
    ID = c("b", "b", "b", "a", "a", "c"),
    a = 1:6, 
    b = 7:12, 
    c = 13:18
)
DT
```

    ##        ID     a     b     c
    ##    <char> <int> <int> <int>
    ## 1:      b     1     7    13
    ## 2:      b     2     8    14
    ## 3:      b     3     9    15
    ## 4:      a     4    10    16
    ## 5:      a     5    11    17
    ## 6:      c     6    12    18

``` r
class(DT$ID)
```

    ## [1] "character"

- `fread()`: create a `data.table` from existing files, as shown in the
  [Data](#data) section above.

- `setDT()`: convert existing objects such as `data.frame`s and `list`s
  to a `data.table`.

- `as.data.table()`: convert existing objects of other structures to a
  `data.table`.

> Note that:
>
> - Unlike `data.frame`s, columns of `character` type are *never*
>   converted to `factors` by default.  
> - Row numbers are printed with a `:`.  
> - `data.table` does not set or use *row* names.  
> - `data.table` automatically prints only the top 5 and bottom 5 rows
>   if the number of rows exceeds the global option
>   `datatable.print.nrows` (default = 100).

### b) General form of `data.table` syntax

    DT[i, j, by]  

    ##   R:                 i                 j        by
    ## SQL:  where | order by   select | update  group by

**The way to read it is:**  
Take `DT`, subset/reorder rows using `i`, then calculate `j`, grouped by
`by`.

> We might also refer to writing thins inside `DT[...]` as querying
> `DT`.

### c) Subset rows in `i`

#### - Get all flights with “JFK” as the origin airport in the month of June

``` r
ans <- flights[origin == "JFK" & month == 6L]
head(ans)
```

    ##     year month   day dep_delay arr_delay carrier origin   dest air_time
    ##    <int> <int> <int>     <int>     <int>  <char> <char> <char>    <int>
    ## 1:  2014     6     1        -9        -5      AA    JFK    LAX      324
    ## 2:  2014     6     1       -10       -13      AA    JFK    LAX      329
    ## 3:  2014     6     1        18        -1      AA    JFK    LAX      326
    ## 4:  2014     6     1        -6       -16      AA    JFK    LAX      320
    ## 5:  2014     6     1        -4       -45      AA    JFK    LAX      326
    ## 6:  2014     6     1        -6       -23      AA    JFK    LAX      329
    ##    distance  hour
    ##       <int> <int>
    ## 1:     2475     8
    ## 2:     2475    12
    ## 3:     2475     7
    ## 4:     2475    10
    ## 5:     2475    18
    ## 6:     2475    14

- Within the frame of a `data.table`, **columns can be referred to as if
  they are variables**.  
  We do not need to add prefix `flights$` each time.  
- The *row indices* that satisfy the condition
  `origin == "JFK" & month == 6L` are computed, and since there is
  nothing else left to do, all columns from `flights` at selected rows
  are simply returned as a `data.table`.  
- A comma after the condition in `i` is not required. But
  `flights[origin == "JFK" & month == 6L, ]` would work just fine.

#### - Get the first two rows from `flights`

``` r
ans <- flights[1:2]
ans
```

    ##     year month   day dep_delay arr_delay carrier origin   dest air_time
    ##    <int> <int> <int>     <int>     <int>  <char> <char> <char>    <int>
    ## 1:  2014     1     1        14        13      AA    JFK    LAX      359
    ## 2:  2014     1     1        -3        13      AA    JFK    LAX      363
    ##    distance  hour
    ##       <int> <int>
    ## 1:     2475     9
    ## 2:     2475    11

- In this case, the row indices are provided in `i`.

#### Sort `flights` first by column `origin` in *ascending* order, and then by `dest` in *descending* order

We can use `order()` to accomplish this.

``` r
ans <- flights[order(origin, -dest)] 
head(ans)
```

    ##     year month   day dep_delay arr_delay carrier origin   dest air_time
    ##    <int> <int> <int>     <int>     <int>  <char> <char> <char>    <int>
    ## 1:  2014     1     5         6        49      EV    EWR    XNA      195
    ## 2:  2014     1     6         7        13      EV    EWR    XNA      190
    ## 3:  2014     1     7        -6       -13      EV    EWR    XNA      179
    ## 4:  2014     1     8        -7       -12      EV    EWR    XNA      184
    ## 5:  2014     1     9        16         7      EV    EWR    XNA      181
    ## 6:  2014     1    13        66        66      EV    EWR    XNA      188
    ##    distance  hour
    ##       <int> <int>
    ## 1:     1131     8
    ## 2:     1131     8
    ## 3:     1131     8
    ## 4:     1131     8
    ## 5:     1131     8
    ## 6:     1131     9

**`order()` is internally optimized**

- we can use `-` on a `character` columns within the frame of a
  `data.table` to sort in *decreasing* order.

### d) Select column(s) in `j`

#### - Select `arr_delay` column, but return it as a vector

``` r
ans <- flights[, arr_delay]
head(ans)
```

    ## [1]  13  13   9 -26   1   0

- Since we want *all the rows*, we simply skip `i`.

#### - Select `arr_delay` column, but return as a `data.table` instead.

``` r
ans <- flights[, list(arr_delay)]
head(ans)
```

    ##    arr_delay
    ##        <int>
    ## 1:        13
    ## 2:        13
    ## 3:         9
    ## 4:       -26
    ## 5:         1
    ## 6:         0

- We wrap the *variables* (column names) within `list()`, which ensures
  that a data.table is returned.  
- `data.table` also allows wrapping columns with `.()` instead of
  `list()`. The former is an alias for the latter. They both mean the
  same.

``` r
ans <- flights[, .(arr_delay)]
head(ans)
```

    ##    arr_delay
    ##        <int>
    ## 1:        13
    ## 2:        13
    ## 3:         9
    ## 4:       -26
    ## 5:         1
    ## 6:         0

> **Tips:**  
> As long as `j-expression` returns a `list`, each element of the list
> will be converted to a column in the resulting `data.table`.

#### - Select both `arr_delay` and `dep_delay` columns

``` r
ans <- flights[, .(arr_delay, dep_delay)]
head(ans)
```

    ##    arr_delay dep_delay
    ##        <int>     <int>
    ## 1:        13        14
    ## 2:        13        -3
    ## 3:         9         2
    ## 4:       -26        -8
    ## 5:         1         2
    ## 6:         0         4

``` r
## alternative
# ans <- flights[, list(arr_delay, dep_delay)]
```

#### - Select both `arr_delay` and `dep_delay` columns and rename them to `delay_arr` and `delay_dep`

Since `.()` is just an alias for `list()`, we can name columns as we
would while creating a `list`

``` r
ans <- flights[, .(delay_arr = arr_delay, delay_dep = dep_delay)]
head(ans)
```

    ##    delay_arr delay_dep
    ##        <int>     <int>
    ## 1:        13        14
    ## 2:        13        -3
    ## 3:         9         2
    ## 4:       -26        -8
    ## 5:         1         2
    ## 6:         0         4

### e) Compute or *do* in `j`

#### - How may trips have had total delay \< 0?

``` r
ans <- flights[, sum( (arr_delay + dep_delay) < 0 )]
ans
```

    ## [1] 141814

- `data.table`’s `j` can handle expressions as well, i.e., *computing on
  columns*.

### f) Subset in `i` and do in `j`

#### - Calculate the average arrival and departure delay for all flights with “JFK” as the origin airport in the month of June.

``` r
ans <- flights[origin == "JFK" & month == 6L, 
               .(m_arr = mean(arr_delay), m_dep = mean(dep_delay))]
ans
```

    ##       m_arr    m_dep
    ##       <num>    <num>
    ## 1: 5.839349 9.807884

- We first subset in `i` to find matching *row indices*. We *do not*
  subset the entire `data.table` corresponding to those rows yet.
- In `j`, we only uses two columns. And what we have to do is to compute
  their `mean()`. Therefore we subset just those columns corresponding
  to the matching rows, and compute their `mean()`.

> Note: `data.table` can see all three main components of the query
> (`i`, `j`, and `by`) altogether before evaluation, not each
> separately.

#### - How many trips have been made in 2014 from “JFK” airport in the month of June?

``` r
ans <- flights[origin == "JFK" & month == 6L, length(dest)]
ans
```

    ## [1] 8422

Here, `length()` is just a function to count the number of rows; its
input argument can be any column names other than `origin` and `month`.

#### Special symbol `.N`:

We can accomplish the same task by using `.N` as follows:

``` r
ans <- flights[origin == "JFK" & month == 6L, .N] 
ans
```

    ## [1] 8422

- `.N` is a special built-in variable that holds the number of
  observations *in the current group*. It is particularly useful when
  combined with `by` as we will see in the next section.  
- Note that we did not warp `.N` with `list()` or `.()`, so a vector is
  returned.

### g) Refer to columns by names in `j` (like in a `data.frame`)

If you are writing out the column names explicitely, there is not
difference from a `data.frame`

#### - Select both `arr_delay` and `dep_delay` columns in the `data.frame` way

``` r
ans <- flights[, c("arr_delay", "dep_delay")]
head(ans)
```

    ##    arr_delay dep_delay
    ##        <int>     <int>
    ## 1:        13        14
    ## 2:        13        -3
    ## 3:         9         2
    ## 4:       -26        -8
    ## 5:         1         2
    ## 6:         0         4

#### - Select the desired columns in a character vector

There are two options: Using the `..` prefix or the `with` argument.

**Option 1:** using the `..` prefix

``` r
select_cols = c("arr_delay", "dep_delay")
flights[, ..select_cols] 
```

    ##         arr_delay dep_delay
    ##             <int>     <int>
    ##      1:        13        14
    ##      2:        13        -3
    ##      3:         9         2
    ##      4:       -26        -8
    ##      5:         1         2
    ##     ---                    
    ## 253312:       -30         1
    ## 253313:       -14        -5
    ## 253314:        16        -8
    ## 253315:        15        -4
    ## 253316:         1        -5

- Similar to Unix terminal, the `..` prefix refers to “up-one-level”
  command.

**Option 2:** using `with = FALSE` argument

``` r
flights[, select_cols, with = FALSE]
```

    ##         arr_delay dep_delay
    ##             <int>     <int>
    ##      1:        13        14
    ##      2:        13        -3
    ##      3:         9         2
    ##      4:       -26        -8
    ##      5:         1         2
    ##     ---                    
    ## 253312:       -30         1
    ## 253313:       -14        -5
    ## 253314:        16        -8
    ## 253315:        15        -4
    ## 253316:         1        -5

- The argument is named `with` after the R function `with()` because of
  simlar functionality.

``` r
DF <- data.frame(x = c(1, 1, 1, 2, 2, 3, 3, 3), y = 1:8)

## (1) normal way
DF[DF$x > 1, ]
```

    ##   x y
    ## 4 2 4
    ## 5 2 5
    ## 6 3 6
    ## 7 3 7
    ## 8 3 8

``` r
## (2) using with 
DF[with(DF, x > 1), ]
```

    ##   x y
    ## 4 2 4
    ## 5 2 5
    ## 6 3 6
    ## 7 3 7
    ## 8 3 8

- Using `with()` in (2) allows using `DF`’s column `x` as if it were a
  variable.  
- Similarly, setting `with = FALSE` disables the ability to refer to
  columns as if they are variables.
  - `with = TRUE` is the default in `data.table` because we can do much
    more by allowing `j` to handle expressions.

We can also **deselect** columns using `-` or `!`. For example:

``` r
## not run

# return all columns except arr_delay and dep_delay 
ans <- flights[, !c("arr_delay", "dep_delay")]
# or 
ans <- flights[, -c("arr_delay", "dep_delay")]
```

We can also select by specifying starting and end column names:

``` r
## not run 

# returns year, month and day
ans <- flights[, year:day] 
# returns day, month and year (reverse order)
ans <- flights[, day:year]
# return all columns except year, month and day
ans <- flights[, -(year:day)]
ans <- flights[, !(year:day)]
```

## 2. Aggregations

### a) Grouping using `by`

#### - How can we get the number of trips corresponding to each origin airport?

``` r
ans <- flights[, .(.N), by = .(origin)]
ans
```

    ##    origin     N
    ##    <char> <int>
    ## 1:    JFK 81483
    ## 2:    LGA 84433
    ## 3:    EWR 87400

``` r
## or equivalently using a character vector in `by`
# ans <- flights[, .(.N), by = "origin"]
```

- Grouping by `origin` obtains the number of rows, `.N`, for each group.

- `by` also accepts a character vector of column names.

- When there is only one column or expression to refer to in `j` and
  `by`, se can drop the `.()` notation.

``` r
ans <- flights[, .N, by = origin] 
ans
```

    ##    origin     N
    ##    <char> <int>
    ## 1:    JFK 81483
    ## 2:    LGA 84433
    ## 3:    EWR 87400

#### - How can we calculate the number of trips for each origin airport for carrier code `"AA"`?

``` r
ans <- flights[carrier == "AA", .N, by = origin] 
ans
```

    ##    origin     N
    ##    <char> <int>
    ## 1:    JFK 11923
    ## 2:    LGA 11730
    ## 3:    EWR  2649

- We first obtain the row indices for the expression `carrier == "AA"`
  from `i`.  
- Using those *row indices*, we obtain the number of rows while grouped
  by `origin`.

#### - How can we get the total number of trips for each `origin, dest` pair for carrier code `"AA"`?

``` r
ans <- flights[carrier == "AA", .N, by = .(origin, dest)]
head(ans)
```

    ##    origin   dest     N
    ##    <char> <char> <int>
    ## 1:    JFK    LAX  3387
    ## 2:    LGA    PBI   245
    ## 3:    EWR    LAX    62
    ## 4:    JFK    MIA  1876
    ## 5:    JFK    SEA   298
    ## 6:    EWR    MIA   848

``` r
## or equivalently using a character vector in 'by'
# ans <- flights[carrier == "AA", .N, by = c("origin", "dest")]
```

#### - How can we get the average arrival and departure delay for each `origin, dest` pair for each month for carrier code `"AA"`?

``` r
ans <- flights[carrier == "AA", 
               .(mean(arr_delay), mean(dep_delay)), 
               by = .(origin, dest, month)]
ans
```

    ##      origin   dest month         V1         V2
    ##      <char> <char> <int>      <num>      <num>
    ##   1:    JFK    LAX     1   6.590361 14.2289157
    ##   2:    LGA    PBI     1  -7.758621  0.3103448
    ##   3:    EWR    LAX     1   1.366667  7.5000000
    ##   4:    JFK    MIA     1  15.720670 18.7430168
    ##   5:    JFK    SEA     1  14.357143 30.7500000
    ##  ---                                          
    ## 196:    LGA    MIA    10  -6.251799 -1.4208633
    ## 197:    JFK    MIA    10  -1.880184  6.6774194
    ## 198:    EWR    PHX    10  -3.032258 -4.2903226
    ## 199:    JFK    MCO    10 -10.048387 -1.6129032
    ## 200:    JFK    DCA    10  16.483871 15.5161290

- Since we did not provide column names for the expressions in `j`, they
  were automatically generated as `V1` and `V2`.  
- Note that the input order of grouping columns is preserved in the
  result.

### b) Sorted `by`: `keyby`

#### - How can we directly order by all the grouping variables?

``` r
ans <- flights[carrier == "AA", 
               .(mean(arr_delay), mean(dep_delay)),
               keyby = .(origin, dest, month)]
ans
```

    ## Key: <origin, dest, month>
    ##      origin   dest month         V1         V2
    ##      <char> <char> <int>      <num>      <num>
    ##   1:    EWR    DFW     1   6.427673 10.0125786
    ##   2:    EWR    DFW     2  10.536765 11.3455882
    ##   3:    EWR    DFW     3  12.865031  8.0797546
    ##   4:    EWR    DFW     4  17.792683 12.9207317
    ##   5:    EWR    DFW     5  18.487805 18.6829268
    ##  ---                                          
    ## 196:    LGA    PBI     1  -7.758621  0.3103448
    ## 197:    LGA    PBI     2  -7.865385  2.4038462
    ## 198:    LGA    PBI     3  -5.754098  3.0327869
    ## 199:    LGA    PBI     4 -13.966667 -4.7333333
    ## 200:    LGA    PBI     5 -10.357143 -6.8571429

- `keyby` automatically orders the result by the grouping variables in
  increasing order.
- **Keys:** `keyby` does more than *just ordering*. Learn more about
  `keys` in the [*“Keys and fast binary search based
  subset”*](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-keys-fast-subset.html)
  vignette.

### c) Chaining

Let’s reconsider the task of getting the total number of trips for each
`origin, dest` pair for carrier code `"AA"`:

``` r
ans <- flights[carrier == "AA", .N, by = .(origin, dest)]
```

#### - How can we order `ans` using the columns `origin` in ascending order, and `dest` in desceding order?

One straightforward way is to store the intermediate result in a
variable and then use `order()` on that variable:

``` r
ans <- ans[order(origin, -dest)]
head(ans)
```

    ##    origin   dest     N
    ##    <char> <char> <int>
    ## 1:    EWR    PHX   121
    ## 2:    EWR    MIA   848
    ## 3:    EWR    LAX    62
    ## 4:    EWR    DFW  1618
    ## 5:    JFK    STT   229
    ## 6:    JFK    SJU   690

But the previous approach requires having to assign the intermediate
result and then overwrite that result. We can avoid this intermediate
assignment to a temporary variable altergether by *chaining*
expressions.

``` r
ans <- flights[carrier == "AA", .N, by = .(origin, dest)][order(origin, -dest)]
head(ans, 10)
```

    ##     origin   dest     N
    ##     <char> <char> <int>
    ##  1:    EWR    PHX   121
    ##  2:    EWR    MIA   848
    ##  3:    EWR    LAX    62
    ##  4:    EWR    DFW  1618
    ##  5:    JFK    STT   229
    ##  6:    JFK    SJU   690
    ##  7:    JFK    SFO  1312
    ##  8:    JFK    SEA   298
    ##  9:    JFK    SAN   299
    ## 10:    JFK    ORD   432

- We can tack expression one from another, forming a chain of
  operations, i.e., `DT[...][...][...]`.
- Or we can also chain them vertically:

<!-- -->

        DT[...
          ][...
           ][...
            ]

### d) Expressions in `by`

If we would like to find out how many flights started late but arrived
earlier (or on time), started and arrived late, etc…

``` r
ans <- flights[, .N, by = .(dep_delay > 0, arr_delay > 0)]
ans
```

    ##    dep_delay arr_delay      N
    ##       <lgcl>    <lgcl>  <int>
    ## 1:      TRUE      TRUE  72836
    ## 2:     FALSE      TRUE  34583
    ## 3:     FALSE     FALSE 119304
    ## 4:      TRUE     FALSE  26593

- The last row corresponds to `dep_delay > 0 = TRUE` and
  `arr_delay > 0 = FALSE`. We can see that 26593 flights started late
  but arrived early (on time).

- Note that we did not provide any names to the `by-expression`.
  Therefore, names have been automatically assigned in the result. As
  with `j`, you can name these expressions as you would elements of any
  `list`, e.g.,
  `DT[, .N, .(dep_delayed = dep_delay > 0, arr_delayed = arr_delay > 0)]`.

- You can provide other columns along with expressions, for example:
  `DT[, .N, by = .(a, b > 0)]`.

### e) Multiple columns in `j`- `.SD`

#### - Do we have to compute `mean()` for each column individually?

No necessarily! Refresh on th **tip** in Section [1d)](#1d) - *“As long
as the `j`- expression returns a `list`,* *each element of the `list`
will be converted to a column in the resulting `data.table`”*.

Suppose we can refer to the *data subset* for each group as a variable
*while grouping*, then we can loop through all the columns of that
variable using the already- or soon-to-be-familiar base function
`lapply()`.

#### Special symbol `.SD`:

`.SD` stands for **S**ubset of **D**ata. It by itself is a `data.table`
that holds the data for the *current group* defined by using *by*.

Let’s use the `data.table` `DT` created at the beginning to get a
glimpse of what `.SD` looks like.

``` r
DT
```

    ##        ID     a     b     c
    ##    <char> <int> <int> <int>
    ## 1:      b     1     7    13
    ## 2:      b     2     8    14
    ## 3:      b     3     9    15
    ## 4:      a     4    10    16
    ## 5:      a     5    11    17
    ## 6:      c     6    12    18

``` r
DT[, print(.SD), by = ID]
```

    ##        a     b     c
    ##    <int> <int> <int>
    ## 1:     1     7    13
    ## 2:     2     8    14
    ## 3:     3     9    15
    ##        a     b     c
    ##    <int> <int> <int>
    ## 1:     4    10    16
    ## 2:     5    11    17
    ##        a     b     c
    ##    <int> <int> <int>
    ## 1:     6    12    18

    ## Empty data.table (0 rows and 1 cols): ID

- `.SD` contains all columns except the *grouping columns* by default.
- It is also generated by preserving the original order - data
  corresponding to `ID = "b"`, then `ID = "a"`, and then `ID = "c"`.

To compute on (multiple) columns, we can then simply use the base R
function `lapply()`.

``` r
DT[, lapply(.SD, mean), by = ID]
```

    ##        ID     a     b     c
    ##    <char> <num> <num> <num>
    ## 1:      b   2.0   8.0  14.0
    ## 2:      a   4.5  10.5  16.5
    ## 3:      c   6.0  12.0  18.0

- `.SD` holds the rows corresponding to columns `a`, `b`, and `c` for
  that group. We compute the `mean()` on each of these columns using
  `lapply()`.

- Each group returns a list of three elements containing the mean value
  which will become the columns of the resulting `data.table`.

- Since `lapply()` returns a list, so there is no need to wrap it with
  an additional `.()`.

#### - How can we specifying just the columns we would like to compute the `mean()` on?

#### .SDcols

Using the argument `.SDcols`. It accepts either column names or column
indices. For example, `.SDcols=c("arr_delay", "dep_delay")` ensures that
`.SD` contains only these two columns for each group.

Similar to part [1g)](#1g), you can also provide the columns to remove
instead of columns to keep using `-` or `!` sign as well as select
consecutive columns as `colA:colB` and deselect consecutive columns as
`!(colA:colB)` or `-(colA:colB)`.

The following code is to use `.SD` along with `.SDcols` to get the
`mean()` of `arr_delay` and `dep_delay` columns grouped by `origin`,
`dest`, and `month`.

``` r
flights[carrier == "AA",                        ## Only on trips with carrier "AA"
        lapply(.SD, mean),                      ## compute the mean
        by = .(origin, dest, month),            ## for every 'origin, dest, month'
        .SDcols = c("arr_delay", "dep_delay")]  ## for just those specified in .SDcols
```

    ##      origin   dest month  arr_delay  dep_delay
    ##      <char> <char> <int>      <num>      <num>
    ##   1:    JFK    LAX     1   6.590361 14.2289157
    ##   2:    LGA    PBI     1  -7.758621  0.3103448
    ##   3:    EWR    LAX     1   1.366667  7.5000000
    ##   4:    JFK    MIA     1  15.720670 18.7430168
    ##   5:    JFK    SEA     1  14.357143 30.7500000
    ##  ---                                          
    ## 196:    LGA    MIA    10  -6.251799 -1.4208633
    ## 197:    JFK    MIA    10  -1.880184  6.6774194
    ## 198:    EWR    PHX    10  -3.032258 -4.2903226
    ## 199:    JFK    MCO    10 -10.048387 -1.6129032
    ## 200:    JFK    DCA    10  16.483871 15.5161290

### f) Subset `.SD` for each group

#### - How can we return the first two rows for each `month`?

``` r
ans <- flights[, head(.SD, 2), by = month]
head(ans)
```

    ##    month  year   day dep_delay arr_delay carrier origin   dest air_time
    ##    <int> <int> <int>     <int>     <int>  <char> <char> <char>    <int>
    ## 1:     1  2014     1        14        13      AA    JFK    LAX      359
    ## 2:     1  2014     1        -3        13      AA    JFK    LAX      363
    ## 3:     2  2014     1        -1         1      AA    JFK    LAX      358
    ## 4:     2  2014     1        -5         3      AA    JFK    LAX      358
    ## 5:     3  2014     1       -11        36      AA    JFK    LAX      375
    ## 6:     3  2014     1        -3        14      AA    JFK    LAX      368
    ##    distance  hour
    ##       <int> <int>
    ## 1:     2475     9
    ## 2:     2475    11
    ## 3:     2475     8
    ## 4:     2475    11
    ## 5:     2475     8
    ## 6:     2475    11

- `.SD` is a `data.table` that holds all the row for *that group*. We
  simply subset the first two rows as we have seen somewhere above this
  page…

- For each group, `head(.SD, 2)` returns the first two rows as a
  data.table, which is also a `list`, so we do not have to wrap it with
  `.()`

### g) Why keep `j` so flexible?

So that we have a consistent syntax and keep using already existing (and
familiar) base functions instead of learning new functions.

#### - How can we concatenate columns A and B for each group in `ID`?

``` r
DT
```

    ##        ID     a     b     c
    ##    <char> <int> <int> <int>
    ## 1:      b     1     7    13
    ## 2:      b     2     8    14
    ## 3:      b     3     9    15
    ## 4:      a     4    10    16
    ## 5:      a     5    11    17
    ## 6:      c     6    12    18

``` r
DT[, .(val = c(a, b)), by = ID]
```

    ##         ID   val
    ##     <char> <int>
    ##  1:      b     1
    ##  2:      b     2
    ##  3:      b     3
    ##  4:      b     7
    ##  5:      b     8
    ##  6:      b     9
    ##  7:      a     4
    ##  8:      a     5
    ##  9:      a    10
    ## 10:      a    11
    ## 11:      c     6
    ## 12:      c    12

#### - What if we would like to have all the values of column `a` and `b` concatenated, but returned as a list column?

``` r
DT[, .(val = list(c(a, b))), by = ID] 
```

    ##        ID         val
    ##    <char>      <list>
    ## 1:      b 1,2,3,7,8,9
    ## 2:      a  4, 5,10,11
    ## 3:      c        6,12

- Here, we first concatenate the values with `c(a, b)` for each group,
  and wrap that with `list()`. So for each group, we return a list of
  all concatinated values.

- Note those commas are for display only. A list column can contain any
  object in each cell, and in this example, each cell is itself a vector
  and some cells contain longer vectors than others.

Well, `j` is a powerful syntax..! Some example:

``` r
## (1) look at the difference between 
DT[, print(c(a, b)), by = ID] 
```

    ## [1] 1 2 3 7 8 9
    ## [1]  4  5 10 11
    ## [1]  6 12

    ## Empty data.table (0 rows and 1 cols): ID

``` r
## (2) and 
DT[, print(list(c(a, b))), by = ID]
```

    ## [[1]]
    ## [1] 1 2 3 7 8 9
    ## 
    ## [[1]]
    ## [1]  4  5 10 11
    ## 
    ## [[1]]
    ## [1]  6 12

    ## Empty data.table (0 rows and 1 cols): ID

In (1), for each group, a vector is returned, with length = 6, 4, 2
here. However, (2) returns a list of length(1) for each group, with its
first element holding vectors of length 6, 4, 2. Therefore (1) results
in a length of `6+4+2=12`, whereas (2) returns `1+1+1=3`.

------------------------------------------------------------------------

## Summary

The general form of `data.table` syntax is:

    DT[i, j, by]

#### Using `i`:

- We can subset rows similarly to a `data.frame`- except we do not have
  to use `DT$` respectively.  
- We can also sort a `data.table` using `order()`

We can do much more in `i` by keying a `data.table`, which allows fast
subsets and joins. We will see this in the [*“Keys and fast binary
search based
subset”*](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-keys-fast-subset.html)
and [*“Joins and rolling
joins”*](https://cran.r-project.org/web/packages/table.express/vignettes/joins.html)  
vignette.

#### Using `j`:

- Select columns the `data.table` way: `DT[, .(colA, colB)]`.

- Select columns the `data.frame` way: `DT[, c("colA", "colB")]`.

- Compute on the columns: `DT[, .(sum(colA), mean(colB))]`.

- Provide names if necessary:
  `DT[, .(sA = sum(colA), mB = mean(colB))]`.

- Combine with `i`: `DT[colA > value, sum(colB)]`.

#### Using `by`:

- Using `by`, we can group by columns by specifying a *list a columns*
  or *a character vector of column names* or even *expressions*.

- `by` can handle multiple columns and also *expressions*.

- `keyby` groups columns to automatically sort the grouped result.

- Use `.SD` + `.SDcols` in `j` to operate on multiple columns using base
  functions such as `lapply()` and `head()`. Some examples:

  - `DT[, lapply(.SD, fun), by = ..., .SDcols = ...]` - applies `fun` to
    all columns specified in `.SDcols` while grouping by the columns
    specified in `by`.  
  - `DT[, head(.SD, 2), by = ...]` - return the first two rows for each
    group.
  - `DT[col > val, head(.SD, 1), by = ...]` - combine `i` along with `j`
    and `by`.

#### Remember the tip:

As long as `j` returns a `list`, each element of the list will become a
column in the resulting `data.table`.

We will see how to *add/update/delete columns by reference* and how to
combine them with i and by in the [next
vignette](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-reference-semantics.html).

## Links of vignettes

<https://cran.r-project.org/web/packages/data.table/vignettes/>
