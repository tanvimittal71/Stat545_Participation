cm009 Exercises: tidy data
================

``` r
suppressPackageStartupMessages(library(tidyverse))
```

## Reading and Writing Data: Exercises

Make a tibble of letters, their order in the alphabet, and then a
pasting of the two columns together.

``` r
(alph_order <-tibble(letter = letters[1:4],
                    order = 1:4))
```

    ## # A tibble: 4 x 2
    ##   letter order
    ##   <chr>  <int>
    ## 1 a          1
    ## 2 b          2
    ## 3 c          3
    ## 4 d          4

Make a tibble of three names and commute times.

Write the `iris` data frame as a `csv`.

``` r
write_csv(iris,"iris_csv")
```

Write the `iris` data frame to a file delimited by a dollar sign.

``` r
write_delim(iris,"iris.txt",delim="$")
```

Read the dollar-delimited `iris` data to a tibble.

``` r
read_delim("iris.txt",delim="$")
```

    ## Parsed with column specification:
    ## cols(
    ##   Sepal.Length = col_double(),
    ##   Sepal.Width = col_double(),
    ##   Petal.Length = col_double(),
    ##   Petal.Width = col_double(),
    ##   Species = col_character()
    ## )

    ## # A tibble: 150 x 5
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ##           <dbl>       <dbl>        <dbl>       <dbl> <chr>  
    ##  1          5.1         3.5          1.4         0.2 setosa 
    ##  2          4.9         3            1.4         0.2 setosa 
    ##  3          4.7         3.2          1.3         0.2 setosa 
    ##  4          4.6         3.1          1.5         0.2 setosa 
    ##  5          5           3.6          1.4         0.2 setosa 
    ##  6          5.4         3.9          1.7         0.4 setosa 
    ##  7          4.6         3.4          1.4         0.3 setosa 
    ##  8          5           3.4          1.5         0.2 setosa 
    ##  9          4.4         2.9          1.4         0.2 setosa 
    ## 10          4.9         3.1          1.5         0.1 setosa 
    ## # ... with 140 more rows

Read these three LOTR csv’s, saving them to `lotr1`, `lotr2`, and
`lotr3`:

  - <https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Fellowship_Of_The_Ring.csv>
  - <https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Two_Towers.csv>
  - <https://github.com/jennybc/lotr-tidy/blob/master/data/The_Return_Of_The_King.csv>

<!-- end list -->

``` r
lotr1 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Fellowship_Of_The_Ring.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Film = col_character(),
    ##   Race = col_character(),
    ##   Female = col_integer(),
    ##   Male = col_integer()
    ## )

``` r
lotr2 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Two_Towers.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Film = col_character(),
    ##   Race = col_character(),
    ##   Female = col_integer(),
    ##   Male = col_integer()
    ## )

``` r
lotr3 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Return_Of_The_King.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Film = col_character(),
    ##   Race = col_character(),
    ##   Female = col_integer(),
    ##   Male = col_integer()
    ## )

## `gather()`

(Exercises largely based off of Jenny Bryan’s [gather
tutorial](https://github.com/jennybc/lotr-tidy/blob/master/02-gather.md))

This function is useful for making untidy data tidy (so that computers
can more easily crunch the numbers).

1.  Combine the three LOTR untidy tables (`lotr1`, `lotr2`, `lotr3`) to
    a single untidy table by stacking them.

<!-- end list -->

``` r
(lotr_untidy <-bind_rows(lotr1,lotr2,lotr3))
```

    ## # A tibble: 9 x 4
    ##   Film                       Race   Female  Male
    ##   <chr>                      <chr>   <int> <int>
    ## 1 The Fellowship Of The Ring Elf      1229   971
    ## 2 The Fellowship Of The Ring Hobbit     14  3644
    ## 3 The Fellowship Of The Ring Man         0  1995
    ## 4 The Two Towers             Elf       331   513
    ## 5 The Two Towers             Hobbit      0  2463
    ## 6 The Two Towers             Man       401  3589
    ## 7 The Return Of The King     Elf       183   510
    ## 8 The Return Of The King     Hobbit      2  2673
    ## 9 The Return Of The King     Man       268  2459

2.  Convert to tidy. Also try this by specifying columns as a range, and
    with the `contains()` function.

<!-- end list -->

``` r
gather(lotr_untidy,key="Gender",value="Word",Female,Male)
```

    ## # A tibble: 18 x 4
    ##    Film                       Race   Gender  Word
    ##    <chr>                      <chr>  <chr>  <int>
    ##  1 The Fellowship Of The Ring Elf    Female  1229
    ##  2 The Fellowship Of The Ring Hobbit Female    14
    ##  3 The Fellowship Of The Ring Man    Female     0
    ##  4 The Two Towers             Elf    Female   331
    ##  5 The Two Towers             Hobbit Female     0
    ##  6 The Two Towers             Man    Female   401
    ##  7 The Return Of The King     Elf    Female   183
    ##  8 The Return Of The King     Hobbit Female     2
    ##  9 The Return Of The King     Man    Female   268
    ## 10 The Fellowship Of The Ring Elf    Male     971
    ## 11 The Fellowship Of The Ring Hobbit Male    3644
    ## 12 The Fellowship Of The Ring Man    Male    1995
    ## 13 The Two Towers             Elf    Male     513
    ## 14 The Two Towers             Hobbit Male    2463
    ## 15 The Two Towers             Man    Male    3589
    ## 16 The Return Of The King     Elf    Male     510
    ## 17 The Return Of The King     Hobbit Male    2673
    ## 18 The Return Of The King     Man    Male    2459

``` r
gather(lotr_untidy,key="Gender",value="Word",Female:Male)
```

    ## # A tibble: 18 x 4
    ##    Film                       Race   Gender  Word
    ##    <chr>                      <chr>  <chr>  <int>
    ##  1 The Fellowship Of The Ring Elf    Female  1229
    ##  2 The Fellowship Of The Ring Hobbit Female    14
    ##  3 The Fellowship Of The Ring Man    Female     0
    ##  4 The Two Towers             Elf    Female   331
    ##  5 The Two Towers             Hobbit Female     0
    ##  6 The Two Towers             Man    Female   401
    ##  7 The Return Of The King     Elf    Female   183
    ##  8 The Return Of The King     Hobbit Female     2
    ##  9 The Return Of The King     Man    Female   268
    ## 10 The Fellowship Of The Ring Elf    Male     971
    ## 11 The Fellowship Of The Ring Hobbit Male    3644
    ## 12 The Fellowship Of The Ring Man    Male    1995
    ## 13 The Two Towers             Elf    Male     513
    ## 14 The Two Towers             Hobbit Male    2463
    ## 15 The Two Towers             Man    Male    3589
    ## 16 The Return Of The King     Elf    Male     510
    ## 17 The Return Of The King     Hobbit Male    2673
    ## 18 The Return Of The King     Man    Male    2459

``` r
gather(lotr_untidy,key="Gender",value="Word",contains("ale"))
```

    ## # A tibble: 18 x 4
    ##    Film                       Race   Gender  Word
    ##    <chr>                      <chr>  <chr>  <int>
    ##  1 The Fellowship Of The Ring Elf    Female  1229
    ##  2 The Fellowship Of The Ring Hobbit Female    14
    ##  3 The Fellowship Of The Ring Man    Female     0
    ##  4 The Two Towers             Elf    Female   331
    ##  5 The Two Towers             Hobbit Female     0
    ##  6 The Two Towers             Man    Female   401
    ##  7 The Return Of The King     Elf    Female   183
    ##  8 The Return Of The King     Hobbit Female     2
    ##  9 The Return Of The King     Man    Female   268
    ## 10 The Fellowship Of The Ring Elf    Male     971
    ## 11 The Fellowship Of The Ring Hobbit Male    3644
    ## 12 The Fellowship Of The Ring Man    Male    1995
    ## 13 The Two Towers             Elf    Male     513
    ## 14 The Two Towers             Hobbit Male    2463
    ## 15 The Two Towers             Man    Male    3589
    ## 16 The Return Of The King     Elf    Male     510
    ## 17 The Return Of The King     Hobbit Male    2673
    ## 18 The Return Of The King     Man    Male    2459

3.  Try again (bind and tidy the three untidy data frames), but without
    knowing how many tables there are originally.
      - The additional work here does not require any additional tools
        from the tidyverse, but instead uses a `do.call` from base R – a
        useful tool in data analysis when the number of “items” is
        variable/unknown, or quite large.

<!-- end list -->

``` r
lotr_list <- list(lotr1,lotr2,lotr3)
do.call(bind_rows,lotr_list)
```

    ## # A tibble: 9 x 4
    ##   Film                       Race   Female  Male
    ##   <chr>                      <chr>   <int> <int>
    ## 1 The Fellowship Of The Ring Elf      1229   971
    ## 2 The Fellowship Of The Ring Hobbit     14  3644
    ## 3 The Fellowship Of The Ring Man         0  1995
    ## 4 The Two Towers             Elf       331   513
    ## 5 The Two Towers             Hobbit      0  2463
    ## 6 The Two Towers             Man       401  3589
    ## 7 The Return Of The King     Elf       183   510
    ## 8 The Return Of The King     Hobbit      2  2673
    ## 9 The Return Of The King     Man       268  2459

## `spread()`

(Exercises largely based off of Jenny Bryan’s [spread
tutorial](https://github.com/jennybc/lotr-tidy/blob/master/03-spread.md))

This function is useful for making tidy data untidy (to be more pleasing
to the eye).

Read in the tidy LOTR data (despite having just made
it):

``` r
lotr_tidy <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/lotr_tidy.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Film = col_character(),
    ##   Race = col_character(),
    ##   Gender = col_character(),
    ##   Words = col_integer()
    ## )

Get word counts across “Race”. Then try “Gender”.

``` r
spread(lotr_tidy, key = "Race", value = "Words")
```

    ## # A tibble: 6 x 5
    ##   Film                       Gender   Elf Hobbit   Man
    ##   <chr>                      <chr>  <int>  <int> <int>
    ## 1 The Fellowship Of The Ring Female  1229     14     0
    ## 2 The Fellowship Of The Ring Male     971   3644  1995
    ## 3 The Return Of The King     Female   183      2   268
    ## 4 The Return Of The King     Male     510   2673  2459
    ## 5 The Two Towers             Female   331      0   401
    ## 6 The Two Towers             Male     513   2463  3589

Now try combining race and gender. Use `unite()` from `tidyr` instead of
`paste()`.

``` r
lotr_tidy %>%
  unite(Race_Gender, Race, Gender) %>% 
  spread(key = "Race_Gender", value = "Words")
```

    ## # A tibble: 3 x 7
    ##   Film   Elf_Female Elf_Male Hobbit_Female Hobbit_Male Man_Female Man_Male
    ##   <chr>       <int>    <int>         <int>       <int>      <int>    <int>
    ## 1 The F~       1229      971            14        3644          0     1995
    ## 2 The R~        183      510             2        2673        268     2459
    ## 3 The T~        331      513             0        2463        401     3589

``` r
lotr_tidy %>% 
  mutate(x = rnorm(nrow(lotr_tidy))) %>% 
  spread(key = "Gender", value = "x")
```

    ## # A tibble: 18 x 5
    ##    Film                       Race   Words   Female     Male
    ##    <chr>                      <chr>  <int>    <dbl>    <dbl>
    ##  1 The Fellowship Of The Ring Elf      971  NA        0.0629
    ##  2 The Fellowship Of The Ring Elf     1229   0.0924  NA     
    ##  3 The Fellowship Of The Ring Hobbit    14  -0.0931  NA     
    ##  4 The Fellowship Of The Ring Hobbit  3644  NA       -0.362 
    ##  5 The Fellowship Of The Ring Man        0  -1.12    NA     
    ##  6 The Fellowship Of The Ring Man     1995  NA       -0.558 
    ##  7 The Return Of The King     Elf      183  -0.701   NA     
    ##  8 The Return Of The King     Elf      510  NA        0.173 
    ##  9 The Return Of The King     Hobbit     2   0.478   NA     
    ## 10 The Return Of The King     Hobbit  2673  NA        0.270 
    ## 11 The Return Of The King     Man      268  -1.95    NA     
    ## 12 The Return Of The King     Man     2459  NA       -1.08  
    ## 13 The Two Towers             Elf      331   0.443   NA     
    ## 14 The Two Towers             Elf      513  NA       -2.84  
    ## 15 The Two Towers             Hobbit     0  -0.0248  NA     
    ## 16 The Two Towers             Hobbit  2463  NA       -1.78  
    ## 17 The Two Towers             Man      401  -1.44    NA     
    ## 18 The Two Towers             Man     3589  NA       -0.426

## Other `tidyr` goodies

Check out the Examples in the documentation to explore the following.

`expand` vs `complete` (trim vs keep everything). Together with
`nesting`. Check out the Examples in the `expand` documentation.

`separate_rows`: useful when you have a variable number of entries in a
“cell”.

`unite` and `separate`.

`uncount` (as the opposite of `dplyr::count()`)

`drop_na` and `replace_na`

`fill`

`full_seq`

## Time remaining?

Time permitting, do [this
exercise](https://github.com/jennybc/lotr-tidy/blob/master/02-gather.md#exercises)
to practice tidying data.
