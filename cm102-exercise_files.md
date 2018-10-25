---
title: "STAT 547 Class Meeting 02 Worksheet"
output:
  html_document:
    keep_md: TRUE
editor_options: 
  chunk_output_type: inline
---


```r
suppressPackageStartupMessages(library(tidyverse))
library(gapminder)
library(testthat)
```

```
## 
## Attaching package: 'testthat'
```

```
## The following object is masked from 'package:dplyr':
## 
##     matches
```

```
## The following object is masked from 'package:purrr':
## 
##     is_null
```

## Resources

Today's lesson has been drawn from the following resources:

1. Mostly [stat545.com: character data](http://stat545.com/block028_character-data.html)
    - See the ["Resources" section](http://stat545.com/block028_character-data.html#resources) for a more comprehensive listing of resources based on the character problem you're facing. 
2. [Older stat545 notes](http://stat545.com/block022_regular-expression.html)
3. [r4ds: strings](https://r4ds.had.co.nz/strings.html).
3. [`stringr` vignette](https://cran.r-project.org/web/packages/stringr/vignettes/stringr.html)

## Basic String Manipulation

__Goal__: Go over some basic functionality of `stringr`. 

There's that famous sentence about the quick brown fox that contains all letters of the alphabet, although I don't quite remember the sentence. Demo: Check to see if it's in the `sentences` data. Try:

`str_detect(string, pattern)`   
`str_subset(string, pattern)`


```r
(fox <- str_subset(sentences, pattern="fox"))
```

```
## [1] "The quick fox jumped on the sleeping cat."
```

Not quite the sentence I was thinking of. How many words does it contain? Use `str_split(string, pattern)`, noting its output (list).

> output must be a list -- we don't know how many entries each sentence (a single string) will be split into (some are 8 words, some 9, some other lengths etc)


```r
(str_split(fox, pattern = " "))
```

```
## [[1]]
## [1] "The"      "quick"    "fox"      "jumped"   "on"       "the"     
## [7] "sleeping" "cat."
```

Exercise: does this sentence contain all letters of the alphabet? Hints: 

- Split by `""`. 
- Consider putting all in lowercase with `str_to_lower()`.
- Use the base R `table()` function.


```r
fox %>% 
  str_split("") %>% 
  `[[`(1) %>%  #make list format or something of first whatever?
   str_to_lower() %>% 
   table()
```

```
## .
##   . a c d e f g h i j k l m n o p q s t u x 
## 7 1 1 2 1 5 1 1 2 2 1 1 1 1 2 2 2 1 1 3 2 1
```


Working in a data frame? `tidyr` has its own version of this. Here's an example from Resource 1, with the fruit data:


```r
tibble(fruit)
```

```
## # A tibble: 80 x 1
##    fruit       
##    <chr>       
##  1 apple       
##  2 apricot     
##  3 avocado     
##  4 banana      
##  5 bell pepper 
##  6 bilberry    
##  7 blackberry  
##  8 blackcurrant
##  9 blood orange
## 10 blueberry   
## # ... with 70 more rows
```

```r
tibble(fruit) %>%
  separate(fruit, into = c("pre", "post"), sep = " ")
```

```
## Warning: Expected 2 pieces. Missing pieces filled with `NA` in 69 rows [1,
## 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15, 16, 18, 19, 20, 21, 22, 23, 24, ...].
```

```
## # A tibble: 80 x 2
##    pre          post  
##    <chr>        <chr> 
##  1 apple        <NA>  
##  2 apricot      <NA>  
##  3 avocado      <NA>  
##  4 banana       <NA>  
##  5 bell         pepper
##  6 bilberry     <NA>  
##  7 blackberry   <NA>  
##  8 blackcurrant <NA>  
##  9 blood        orange
## 10 blueberry    <NA>  
## # ... with 70 more rows
```

Demo: we can substitute, too. Replace the word "fox" with "giraffe" using `str_replace(string, pattern, replacement)`:


```r
fox %>% 
    str_replace(pattern = "fox", replacement = "giraffe")
```

```
## [1] "The quick giraffe jumped on the sleeping cat."
```

Know the position you want to extract/replace? Try `str_sub()`.

`str_pad()` extends each string to a minimum length:


```r
fruit %>% head
```

```
## [1] "apple"       "apricot"     "avocado"     "banana"      "bell pepper"
## [6] "bilberry"
```

```r
fruit %>% 
    str_pad(width=7, side="right", pad="$") %>% 
    head()
```

```
## [1] "apple$$"     "apricot"     "avocado"     "banana$"     "bell pepper"
## [6] "bilberry"
```

`str_length()` (Not the same as `length()`!)
> str_length() = how many characters in each vector entry (e.g. apple has 5 characters)
> length(fruit) looks at fruit as vector, how many entries are there in vector (e.g. 80 fruits)


```r
str_length(fruit)
```

```
##  [1]  5  7  7  6 11  8 10 12 12  9 11 10 12 10  9  6 12 10 10  7  9  8  7
## [24]  6  4 11  6  8 10  6  3 10 10  5 10  5  8 11  9  6  6 10  7  5  4  6
## [47]  6  9  5  8  9  3  5  6  6  6 12  5  4  9  8  9  4 11  6 17  6  6  8
## [70]  9 10 10 11  7 10 10  9  9 10 10
```

```r
length(fruit)
```

```
## [1] 80
```


`str_c()` for concatenating strings. Check the docs for an excellent explanation using a matrix.


```r
str_c(words[1:4], words[5:8], sep=" & ")
```

```
## [1] "a & accept"        "able & account"    "about & achieve"  
## [4] "absolute & across"
```

```r
str_c(words[3:4], words[5:8], sep=" & ")
```

```
## [1] "about & accept"     "absolute & account" "about & achieve"   
## [4] "absolute & across"
```

```r
str_c(words[3:4], words[5:8], sep=" & ", collapse=", ")
```

```
## [1] "about & accept, absolute & account, about & achieve, absolute & across"
```

There's a (more limited) `tidyr` version. Straight from Resource 1:


```r
fruit_df <- tibble(
  fruit1 = fruit[1:4],
  fruit2 = fruit[5:8]
)
fruit_df %>% 
  unite("flavor_combo", fruit1, fruit2, sep = " & ")
```

```
## # A tibble: 4 x 1
##   flavor_combo         
##   <chr>                
## 1 apple & bell pepper  
## 2 apricot & bilberry   
## 3 avocado & blackberry 
## 4 banana & blackcurrant
```


## Exercise: Populate your Participation Repo

So, you don't want to manually make 12 folders for your participation repo. I hear you. Let's do that by making a character vector with entries `"cm101"`, `"cm102"`, ..., `"cm112"`.

(If you've already done this, it's still a useful exercise!)

### Make Folders

Let's make those folders!

1. Make a character vector with entries `"01"`, `"02"`, ..., `12` with `str_pad()`.


```r
(num <- str_pad(3:12, width = 2, side = "left", pad = "0"))
```

```
##  [1] "03" "04" "05" "06" "07" "08" "09" "10" "11" "12"
```

2. Use `str_c()` to combine `"cm1"` with the numbers:
    - If your system uses "\" instead of "/", you might need two backslashes.


```r
(folders <- str_c("/cm1", num))
```

```
##  [1] "/cm103" "/cm104" "/cm105" "/cm106" "/cm107" "/cm108" "/cm109"
##  [8] "/cm110" "/cm111" "/cm112"
```

3. Use `testthat` to check that each entry of `folders` has 5 characters. You might find the base R `all()` function useful.


```r
test_that("folder names are length 6.", {
    expect_true(all(str_length(folders) == 6))
})

#nothing left, returns test
```

4. BONUS: If applicable, make the folders using `dir.create()`.
    - Note: `dir.create()` requires the full path to be specified. You might find the `here::here()` function useful.
    - This code might work (depending on your directory): `for (folder in folders) dir.create(here::here(folder))`
    - We'll learn how to use `purrr` instead of loops next week.

### Make README's

Now, let's seed the folders with README's.

1. Add `/README.md` to the end of the folder names stored in `folders`:
> SHOULD ALL FOLDERS HAVE A README? 


```r
(files <- str_c(folders, "/README.md"))
```

```
##  [1] "/cm103/README.md" "/cm104/README.md" "/cm105/README.md"
##  [4] "/cm106/README.md" "/cm107/README.md" "/cm108/README.md"
##  [7] "/cm109/README.md" "/cm110/README.md" "/cm111/README.md"
## [10] "/cm112/README.md"
```

2. Make a vector of contents to put in each README. Put a title and body. 
    - Hint: Use `\n` to indicate a new line! This works in graphs, too.
    

```r
contents <- str_c("# Participation\n\n Participation for class meeting ", 3:12)
cat(contents[1])
```

```
## # Participation
## 
##  Participation for class meeting 3
```

3. BONUS: Write the README's to file using base R's `write(x, file)`:
    - `for (i in 1:length(files)) write(contents[i], files[i])`
    - There's a better alternative to a loop using `purrr`. Next week's topic!
    - This code might not work, depending on your workind directory and system.

## Regular Expressions (aka regex)

Great resource is [r4ds](https://r4ds.had.co.nz/strings.html#matching-patterns-with-regular-expressions)!

Premable:

- Useful for identifying _patterns_, not exact character specifications.
- Hard to read and write!
- We'll focus on finding _matches_ (the hardest part). You can also use regex to manipulate strings -- but we'll delegate that to [r4ds: strings: tools](https://r4ds.had.co.nz/strings.html#tools).

Staying true to Resource 1, let's work with the gapminder countries:


```r
library(gapminder)
countries <- levels(gapminder$country)
```

### The "any character"

Find all countries in the gapminder data set with the following pattern: "i", followed by any single character, followed by "a":


```r
str_subset(countries, pattern = "i.a")
```

```
##  [1] "Argentina"                "Bosnia and Herzegovina"  
##  [3] "Burkina Faso"             "Central African Republic"
##  [5] "China"                    "Costa Rica"              
##  [7] "Dominican Republic"       "Hong Kong, China"        
##  [9] "Jamaica"                  "Mauritania"              
## [11] "Nicaragua"                "South Africa"            
## [13] "Swaziland"                "Taiwan"                  
## [15] "Thailand"                 "Trinidad and Tobago"
```

Here, `.` stands for "any single character".

But, where's Italy? Case-sensitive!

Let's use `str_view_all()` to see the matches:


```r
str_view_all(countries, pattern = "i.a")
```

<!--html_preserve--><div id="htmlwidget-9bdfd5c476bd0ae10837" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-9bdfd5c476bd0ae10837">{"x":{"html":"<ul>\n  <li>Afghanistan<\/li>\n  <li>Albania<\/li>\n  <li>Algeria<\/li>\n  <li>Angola<\/li>\n  <li>Argent<span class='match'>ina<\/span><\/li>\n  <li>Australia<\/li>\n  <li>Austria<\/li>\n  <li>Bahrain<\/li>\n  <li>Bangladesh<\/li>\n  <li>Belgium<\/li>\n  <li>Benin<\/li>\n  <li>Bolivia<\/li>\n  <li>Bosnia and Herzegov<span class='match'>ina<\/span><\/li>\n  <li>Botswana<\/li>\n  <li>Brazil<\/li>\n  <li>Bulgaria<\/li>\n  <li>Burk<span class='match'>ina<\/span> Faso<\/li>\n  <li>Burundi<\/li>\n  <li>Cambodia<\/li>\n  <li>Cameroon<\/li>\n  <li>Canada<\/li>\n  <li>Central Afr<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Chad<\/li>\n  <li>Chile<\/li>\n  <li>Ch<span class='match'>ina<\/span><\/li>\n  <li>Colombia<\/li>\n  <li>Comoros<\/li>\n  <li>Congo, Dem. Rep.<\/li>\n  <li>Congo, Rep.<\/li>\n  <li>Costa R<span class='match'>ica<\/span><\/li>\n  <li>Cote d'Ivoire<\/li>\n  <li>Croatia<\/li>\n  <li>Cuba<\/li>\n  <li>Czech Republic<\/li>\n  <li>Denmark<\/li>\n  <li>Djibouti<\/li>\n  <li>Domin<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Ecuador<\/li>\n  <li>Egypt<\/li>\n  <li>El Salvador<\/li>\n  <li>Equatorial Guinea<\/li>\n  <li>Eritrea<\/li>\n  <li>Ethiopia<\/li>\n  <li>Finland<\/li>\n  <li>France<\/li>\n  <li>Gabon<\/li>\n  <li>Gambia<\/li>\n  <li>Germany<\/li>\n  <li>Ghana<\/li>\n  <li>Greece<\/li>\n  <li>Guatemala<\/li>\n  <li>Guinea<\/li>\n  <li>Guinea-Bissau<\/li>\n  <li>Haiti<\/li>\n  <li>Honduras<\/li>\n  <li>Hong Kong, Ch<span class='match'>ina<\/span><\/li>\n  <li>Hungary<\/li>\n  <li>Iceland<\/li>\n  <li>India<\/li>\n  <li>Indonesia<\/li>\n  <li>Iran<\/li>\n  <li>Iraq<\/li>\n  <li>Ireland<\/li>\n  <li>Israel<\/li>\n  <li>Italy<\/li>\n  <li>Jama<span class='match'>ica<\/span><\/li>\n  <li>Japan<\/li>\n  <li>Jordan<\/li>\n  <li>Kenya<\/li>\n  <li>Korea, Dem. Rep.<\/li>\n  <li>Korea, Rep.<\/li>\n  <li>Kuwait<\/li>\n  <li>Lebanon<\/li>\n  <li>Lesotho<\/li>\n  <li>Liberia<\/li>\n  <li>Libya<\/li>\n  <li>Madagascar<\/li>\n  <li>Malawi<\/li>\n  <li>Malaysia<\/li>\n  <li>Mali<\/li>\n  <li>Maur<span class='match'>ita<\/span>nia<\/li>\n  <li>Mauritius<\/li>\n  <li>Mexico<\/li>\n  <li>Mongolia<\/li>\n  <li>Montenegro<\/li>\n  <li>Morocco<\/li>\n  <li>Mozambique<\/li>\n  <li>Myanmar<\/li>\n  <li>Namibia<\/li>\n  <li>Nepal<\/li>\n  <li>Netherlands<\/li>\n  <li>New Zealand<\/li>\n  <li>N<span class='match'>ica<\/span>ragua<\/li>\n  <li>Niger<\/li>\n  <li>Nigeria<\/li>\n  <li>Norway<\/li>\n  <li>Oman<\/li>\n  <li>Pakistan<\/li>\n  <li>Panama<\/li>\n  <li>Paraguay<\/li>\n  <li>Peru<\/li>\n  <li>Philippines<\/li>\n  <li>Poland<\/li>\n  <li>Portugal<\/li>\n  <li>Puerto Rico<\/li>\n  <li>Reunion<\/li>\n  <li>Romania<\/li>\n  <li>Rwanda<\/li>\n  <li>Sao Tome and Principe<\/li>\n  <li>Saudi Arabia<\/li>\n  <li>Senegal<\/li>\n  <li>Serbia<\/li>\n  <li>Sierra Leone<\/li>\n  <li>Singapore<\/li>\n  <li>Slovak Republic<\/li>\n  <li>Slovenia<\/li>\n  <li>Somalia<\/li>\n  <li>South Afr<span class='match'>ica<\/span><\/li>\n  <li>Spain<\/li>\n  <li>Sri Lanka<\/li>\n  <li>Sudan<\/li>\n  <li>Swaz<span class='match'>ila<\/span>nd<\/li>\n  <li>Sweden<\/li>\n  <li>Switzerland<\/li>\n  <li>Syria<\/li>\n  <li>Ta<span class='match'>iwa<\/span>n<\/li>\n  <li>Tanzania<\/li>\n  <li>Tha<span class='match'>ila<\/span>nd<\/li>\n  <li>Togo<\/li>\n  <li>Trin<span class='match'>ida<\/span>d and Tobago<\/li>\n  <li>Tunisia<\/li>\n  <li>Turkey<\/li>\n  <li>Uganda<\/li>\n  <li>United Kingdom<\/li>\n  <li>United States<\/li>\n  <li>Uruguay<\/li>\n  <li>Venezuela<\/li>\n  <li>Vietnam<\/li>\n  <li>West Bank and Gaza<\/li>\n  <li>Yemen, Rep.<\/li>\n  <li>Zambia<\/li>\n  <li>Zimbabwe<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view_all(countries, pattern = "i.a", match=TRUE)
```

<!--html_preserve--><div id="htmlwidget-2378ec22e1d419b6b40a" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-2378ec22e1d419b6b40a">{"x":{"html":"<ul>\n  <li>Argent<span class='match'>ina<\/span><\/li>\n  <li>Bosnia and Herzegov<span class='match'>ina<\/span><\/li>\n  <li>Burk<span class='match'>ina<\/span> Faso<\/li>\n  <li>Central Afr<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Ch<span class='match'>ina<\/span><\/li>\n  <li>Costa R<span class='match'>ica<\/span><\/li>\n  <li>Domin<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Hong Kong, Ch<span class='match'>ina<\/span><\/li>\n  <li>Jama<span class='match'>ica<\/span><\/li>\n  <li>Maur<span class='match'>ita<\/span>nia<\/li>\n  <li>N<span class='match'>ica<\/span>ragua<\/li>\n  <li>South Afr<span class='match'>ica<\/span><\/li>\n  <li>Swaz<span class='match'>ila<\/span>nd<\/li>\n  <li>Ta<span class='match'>iwa<\/span>n<\/li>\n  <li>Tha<span class='match'>ila<\/span>nd<\/li>\n  <li>Trin<span class='match'>ida<\/span>d and Tobago<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Exercise: Canada isn't the only country with three interspersed "a"'s. Find the others. Try both `str_view_all()` and `str_subset()`.


```r
#str_view_all(FILL_THIS_IN)
#str_subset(FILL_THIS_IN)
```


Let's define a handy function:


```r
str_view_all_match <- function(countries, pattern) {
    str_view_all(countries, pattern, match=TRUE)
}
str_view_all_match(countries, pattern = "i.a")
```

<!--html_preserve--><div id="htmlwidget-67ced66ae7abba6d23dd" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-67ced66ae7abba6d23dd">{"x":{"html":"<ul>\n  <li>Argent<span class='match'>ina<\/span><\/li>\n  <li>Bosnia and Herzegov<span class='match'>ina<\/span><\/li>\n  <li>Burk<span class='match'>ina<\/span> Faso<\/li>\n  <li>Central Afr<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Ch<span class='match'>ina<\/span><\/li>\n  <li>Costa R<span class='match'>ica<\/span><\/li>\n  <li>Domin<span class='match'>ica<\/span>n Republic<\/li>\n  <li>Hong Kong, Ch<span class='match'>ina<\/span><\/li>\n  <li>Jama<span class='match'>ica<\/span><\/li>\n  <li>Maur<span class='match'>ita<\/span>nia<\/li>\n  <li>N<span class='match'>ica<\/span>ragua<\/li>\n  <li>South Afr<span class='match'>ica<\/span><\/li>\n  <li>Swaz<span class='match'>ila<\/span>nd<\/li>\n  <li>Ta<span class='match'>iwa<\/span>n<\/li>\n  <li>Tha<span class='match'>ila<\/span>nd<\/li>\n  <li>Trin<span class='match'>ida<\/span>d and Tobago<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


### The escape

What if I wanted to literally search for countries with a period in the name? Escape with `\`, although R requires a double escape.


```r
str_view_all_match(countries, pattern = "\\.")
```

<!--html_preserve--><div id="htmlwidget-cf94562a0e8e2161f645" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-cf94562a0e8e2161f645">{"x":{"html":"<ul>\n  <li>Congo, Dem<span class='match'>.<\/span> Rep<span class='match'>.<\/span><\/li>\n  <li>Congo, Rep<span class='match'>.<\/span><\/li>\n  <li>Korea, Dem<span class='match'>.<\/span> Rep<span class='match'>.<\/span><\/li>\n  <li>Korea, Rep<span class='match'>.<\/span><\/li>\n  <li>Yemen, Rep<span class='match'>.<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Why does R require a double escape? It does one level of escaping before "executing" the regex. 

- See `?Quotes`
- Try searching for "s\. " (without quotes) in this document (don't forget to select "Regex")

### Character Classes

- `[letters]` matches a single character that's either l, e, t, ..., or s.
- `[^letters]`: anything _but_ these letters. 

See more at: https://r4ds.had.co.nz/strings.html#character-classes-and-alternatives

Note that not all special characters "work" within `[]`, but some do, and do not always carry the same meaning (like `^`)! From said resource, they are:

>  `$` `.` `|` `?` `*` `+` `(` `)` `[` `{`. Unfortunately, a few characters have special meaning even inside a character class and must be handled with backslash escapes: `]` `\` `^` and `-`.

Exercise: Find all countries with three non-vowels next to each other.


```r
str_view_all_match(countries, pattern = "YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-e12328765b975ac02ce3" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-e12328765b975ac02ce3">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


### Or

- Use `|` to denote "or". 
- "And" is implied otherwise, and has precedence.
- Use parentheses to indicate precedence.

Beer or bear?


```r
c("bear", "beer", "bar") %>% 
    str_view_all_match(pattern = "be(e|a)r")
```

<!--html_preserve--><div id="htmlwidget-a714d715d01fdc9fc0a0" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-a714d715d01fdc9fc0a0">{"x":{"html":"<ul>\n  <li><span class='match'>bear<\/span><\/li>\n  <li><span class='match'>beer<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


### Quantifiers/Repetition

The handy ones are:

- `*` for 0 or more
- `+` for 1 or more
- `?` for 0 or 1

See list at https://r4ds.had.co.nz/strings.html#repetition

Find all countries that have any number of o's (but at least 1) following r:


```r
str_view_all_match(countries, "re+")
```

<!--html_preserve--><div id="htmlwidget-3cb39d10aa47e6ccb5b6" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-3cb39d10aa47e6ccb5b6">{"x":{"html":"<ul>\n  <li>Cote d'Ivoi<span class='match'>re<\/span><\/li>\n  <li>Erit<span class='match'>re<\/span>a<\/li>\n  <li>G<span class='match'>ree<\/span>ce<\/li>\n  <li>I<span class='match'>re<\/span>land<\/li>\n  <li>Ko<span class='match'>re<\/span>a, Dem. Rep.<\/li>\n  <li>Ko<span class='match'>re<\/span>a, Rep.<\/li>\n  <li>Singapo<span class='match'>re<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


Find all countries that have exactly two e's next two each other:


```r
str_view_all_match(countries, "e{2}")
```

<!--html_preserve--><div id="htmlwidget-f7848d3bae544bee0d68" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-f7848d3bae544bee0d68">{"x":{"html":"<ul>\n  <li>Gr<span class='match'>ee<\/span>ce<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


Exercise: Find all countries that have either "a" or "e", twice in a row (with a changeover allowed, such as "ae" or "ea"):


```r
str_view_all_match(countries, pattern="YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-9988c23c41f4ad23e49a" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-9988c23c41f4ad23e49a">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


### Position indicators

- `^` corresponds to the __beginning__ of the line.
- `$` corresponds to the __end__ of the line.

Countries that end in "land":


```r
str_view_all_match(countries, pattern = "land$")
```

<!--html_preserve--><div id="htmlwidget-36b612c55446f3f512b6" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-36b612c55446f3f512b6">{"x":{"html":"<ul>\n  <li>Fin<span class='match'>land<\/span><\/li>\n  <li>Ice<span class='match'>land<\/span><\/li>\n  <li>Ire<span class='match'>land<\/span><\/li>\n  <li>New Zea<span class='match'>land<\/span><\/li>\n  <li>Po<span class='match'>land<\/span><\/li>\n  <li>Swazi<span class='match'>land<\/span><\/li>\n  <li>Switzer<span class='match'>land<\/span><\/li>\n  <li>Thai<span class='match'>land<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view_all_match(countries, pattern = "$")
```

<!--html_preserve--><div id="htmlwidget-5248d22ca17dcde08c77" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-5248d22ca17dcde08c77">{"x":{"html":"<ul>\n  <li>Afghanistan<span class='match'><\/span><\/li>\n  <li>Albania<span class='match'><\/span><\/li>\n  <li>Algeria<span class='match'><\/span><\/li>\n  <li>Angola<span class='match'><\/span><\/li>\n  <li>Argentina<span class='match'><\/span><\/li>\n  <li>Australia<span class='match'><\/span><\/li>\n  <li>Austria<span class='match'><\/span><\/li>\n  <li>Bahrain<span class='match'><\/span><\/li>\n  <li>Bangladesh<span class='match'><\/span><\/li>\n  <li>Belgium<span class='match'><\/span><\/li>\n  <li>Benin<span class='match'><\/span><\/li>\n  <li>Bolivia<span class='match'><\/span><\/li>\n  <li>Bosnia and Herzegovina<span class='match'><\/span><\/li>\n  <li>Botswana<span class='match'><\/span><\/li>\n  <li>Brazil<span class='match'><\/span><\/li>\n  <li>Bulgaria<span class='match'><\/span><\/li>\n  <li>Burkina Faso<span class='match'><\/span><\/li>\n  <li>Burundi<span class='match'><\/span><\/li>\n  <li>Cambodia<span class='match'><\/span><\/li>\n  <li>Cameroon<span class='match'><\/span><\/li>\n  <li>Canada<span class='match'><\/span><\/li>\n  <li>Central African Republic<span class='match'><\/span><\/li>\n  <li>Chad<span class='match'><\/span><\/li>\n  <li>Chile<span class='match'><\/span><\/li>\n  <li>China<span class='match'><\/span><\/li>\n  <li>Colombia<span class='match'><\/span><\/li>\n  <li>Comoros<span class='match'><\/span><\/li>\n  <li>Congo, Dem. Rep.<span class='match'><\/span><\/li>\n  <li>Congo, Rep.<span class='match'><\/span><\/li>\n  <li>Costa Rica<span class='match'><\/span><\/li>\n  <li>Cote d'Ivoire<span class='match'><\/span><\/li>\n  <li>Croatia<span class='match'><\/span><\/li>\n  <li>Cuba<span class='match'><\/span><\/li>\n  <li>Czech Republic<span class='match'><\/span><\/li>\n  <li>Denmark<span class='match'><\/span><\/li>\n  <li>Djibouti<span class='match'><\/span><\/li>\n  <li>Dominican Republic<span class='match'><\/span><\/li>\n  <li>Ecuador<span class='match'><\/span><\/li>\n  <li>Egypt<span class='match'><\/span><\/li>\n  <li>El Salvador<span class='match'><\/span><\/li>\n  <li>Equatorial Guinea<span class='match'><\/span><\/li>\n  <li>Eritrea<span class='match'><\/span><\/li>\n  <li>Ethiopia<span class='match'><\/span><\/li>\n  <li>Finland<span class='match'><\/span><\/li>\n  <li>France<span class='match'><\/span><\/li>\n  <li>Gabon<span class='match'><\/span><\/li>\n  <li>Gambia<span class='match'><\/span><\/li>\n  <li>Germany<span class='match'><\/span><\/li>\n  <li>Ghana<span class='match'><\/span><\/li>\n  <li>Greece<span class='match'><\/span><\/li>\n  <li>Guatemala<span class='match'><\/span><\/li>\n  <li>Guinea<span class='match'><\/span><\/li>\n  <li>Guinea-Bissau<span class='match'><\/span><\/li>\n  <li>Haiti<span class='match'><\/span><\/li>\n  <li>Honduras<span class='match'><\/span><\/li>\n  <li>Hong Kong, China<span class='match'><\/span><\/li>\n  <li>Hungary<span class='match'><\/span><\/li>\n  <li>Iceland<span class='match'><\/span><\/li>\n  <li>India<span class='match'><\/span><\/li>\n  <li>Indonesia<span class='match'><\/span><\/li>\n  <li>Iran<span class='match'><\/span><\/li>\n  <li>Iraq<span class='match'><\/span><\/li>\n  <li>Ireland<span class='match'><\/span><\/li>\n  <li>Israel<span class='match'><\/span><\/li>\n  <li>Italy<span class='match'><\/span><\/li>\n  <li>Jamaica<span class='match'><\/span><\/li>\n  <li>Japan<span class='match'><\/span><\/li>\n  <li>Jordan<span class='match'><\/span><\/li>\n  <li>Kenya<span class='match'><\/span><\/li>\n  <li>Korea, Dem. Rep.<span class='match'><\/span><\/li>\n  <li>Korea, Rep.<span class='match'><\/span><\/li>\n  <li>Kuwait<span class='match'><\/span><\/li>\n  <li>Lebanon<span class='match'><\/span><\/li>\n  <li>Lesotho<span class='match'><\/span><\/li>\n  <li>Liberia<span class='match'><\/span><\/li>\n  <li>Libya<span class='match'><\/span><\/li>\n  <li>Madagascar<span class='match'><\/span><\/li>\n  <li>Malawi<span class='match'><\/span><\/li>\n  <li>Malaysia<span class='match'><\/span><\/li>\n  <li>Mali<span class='match'><\/span><\/li>\n  <li>Mauritania<span class='match'><\/span><\/li>\n  <li>Mauritius<span class='match'><\/span><\/li>\n  <li>Mexico<span class='match'><\/span><\/li>\n  <li>Mongolia<span class='match'><\/span><\/li>\n  <li>Montenegro<span class='match'><\/span><\/li>\n  <li>Morocco<span class='match'><\/span><\/li>\n  <li>Mozambique<span class='match'><\/span><\/li>\n  <li>Myanmar<span class='match'><\/span><\/li>\n  <li>Namibia<span class='match'><\/span><\/li>\n  <li>Nepal<span class='match'><\/span><\/li>\n  <li>Netherlands<span class='match'><\/span><\/li>\n  <li>New Zealand<span class='match'><\/span><\/li>\n  <li>Nicaragua<span class='match'><\/span><\/li>\n  <li>Niger<span class='match'><\/span><\/li>\n  <li>Nigeria<span class='match'><\/span><\/li>\n  <li>Norway<span class='match'><\/span><\/li>\n  <li>Oman<span class='match'><\/span><\/li>\n  <li>Pakistan<span class='match'><\/span><\/li>\n  <li>Panama<span class='match'><\/span><\/li>\n  <li>Paraguay<span class='match'><\/span><\/li>\n  <li>Peru<span class='match'><\/span><\/li>\n  <li>Philippines<span class='match'><\/span><\/li>\n  <li>Poland<span class='match'><\/span><\/li>\n  <li>Portugal<span class='match'><\/span><\/li>\n  <li>Puerto Rico<span class='match'><\/span><\/li>\n  <li>Reunion<span class='match'><\/span><\/li>\n  <li>Romania<span class='match'><\/span><\/li>\n  <li>Rwanda<span class='match'><\/span><\/li>\n  <li>Sao Tome and Principe<span class='match'><\/span><\/li>\n  <li>Saudi Arabia<span class='match'><\/span><\/li>\n  <li>Senegal<span class='match'><\/span><\/li>\n  <li>Serbia<span class='match'><\/span><\/li>\n  <li>Sierra Leone<span class='match'><\/span><\/li>\n  <li>Singapore<span class='match'><\/span><\/li>\n  <li>Slovak Republic<span class='match'><\/span><\/li>\n  <li>Slovenia<span class='match'><\/span><\/li>\n  <li>Somalia<span class='match'><\/span><\/li>\n  <li>South Africa<span class='match'><\/span><\/li>\n  <li>Spain<span class='match'><\/span><\/li>\n  <li>Sri Lanka<span class='match'><\/span><\/li>\n  <li>Sudan<span class='match'><\/span><\/li>\n  <li>Swaziland<span class='match'><\/span><\/li>\n  <li>Sweden<span class='match'><\/span><\/li>\n  <li>Switzerland<span class='match'><\/span><\/li>\n  <li>Syria<span class='match'><\/span><\/li>\n  <li>Taiwan<span class='match'><\/span><\/li>\n  <li>Tanzania<span class='match'><\/span><\/li>\n  <li>Thailand<span class='match'><\/span><\/li>\n  <li>Togo<span class='match'><\/span><\/li>\n  <li>Trinidad and Tobago<span class='match'><\/span><\/li>\n  <li>Tunisia<span class='match'><\/span><\/li>\n  <li>Turkey<span class='match'><\/span><\/li>\n  <li>Uganda<span class='match'><\/span><\/li>\n  <li>United Kingdom<span class='match'><\/span><\/li>\n  <li>United States<span class='match'><\/span><\/li>\n  <li>Uruguay<span class='match'><\/span><\/li>\n  <li>Venezuela<span class='match'><\/span><\/li>\n  <li>Vietnam<span class='match'><\/span><\/li>\n  <li>West Bank and Gaza<span class='match'><\/span><\/li>\n  <li>Yemen, Rep.<span class='match'><\/span><\/li>\n  <li>Zambia<span class='match'><\/span><\/li>\n  <li>Zimbabwe<span class='match'><\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Countries that start with "Ca":


```r
str_view_all_match(countries, pattern = "^Ca")
```

<!--html_preserve--><div id="htmlwidget-6d8ef61b2b392a3451f5" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-6d8ef61b2b392a3451f5">{"x":{"html":"<ul>\n  <li><span class='match'>Ca<\/span>mbodia<\/li>\n  <li><span class='match'>Ca<\/span>meroon<\/li>\n  <li><span class='match'>Ca<\/span>nada<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Countries without a vowel? The word should start with a non-vowel, continue as a non-vowel, and end:


```r
str_view_all_match(countries, "^[^aeiouAEIOU]*$")
```

<!--html_preserve--><div id="htmlwidget-96b042d49497e142453f" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-96b042d49497e142453f">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

### Groups

We can refer to parentheses groups:


```r
str_view_all(c("abad", "abbd"), pattern="(a)(b)\\1")
```

<!--html_preserve--><div id="htmlwidget-c3e3867482fd21731f85" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-c3e3867482fd21731f85">{"x":{"html":"<ul>\n  <li><span class='match'>aba<\/span>d<\/li>\n  <li>abbd<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view_all(c("abad", "abbd"), pattern="(a)(b)\\2")
```

<!--html_preserve--><div id="htmlwidget-5458f161749ba9ccacf5" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-5458f161749ba9ccacf5">{"x":{"html":"<ul>\n  <li>abad<\/li>\n  <li><span class='match'>abb<\/span>d<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Note that the parentheses are first resolved, THEN referred to. NOT re-executed.


```r
str_view_all(c("bananas"), "(.)(.)\\1\\2.*\\1\\2")
```

<!--html_preserve--><div id="htmlwidget-e7f4caebf2db77ff475d" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-e7f4caebf2db77ff475d">{"x":{"html":"<ul>\n  <li>bananas<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

We can refer to them later in the search, too:


```r
str_view_all(c("bananas", "Who can? Bananas can."), "(.)(.)\\1\\2.*\\1\\2")
```

<!--html_preserve--><div id="htmlwidget-3ad92e7f79bd0d70d5ce" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-3ad92e7f79bd0d70d5ce">{"x":{"html":"<ul>\n  <li>bananas<\/li>\n  <li>Who can? B<span class='match'>ananas can<\/span>.<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


## Final Exercises

Convert `words` to pig latin, which involves:

1. Make the first letter the last letter
    - Get the first letter with `str_sub(string, start, end)`.
2. Remove the first letter from `words`.
    - Hint: leave the `end` argument blank.
3. Add "ay" to the end of the word.
    - Use `str_c()`.
    

```r
#YOUR_CODE_HERE
```

Find all countries that end in "y"


```r
str_view_all_match(countries, "YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-c24e23e956356dce1948" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-c24e23e956356dce1948">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Find all countries that have the same letter repeated twice (like "Greece", which has "ee").


```r
str_view_all_match(countries, "YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-c86dff52b04ed2483137" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-c86dff52b04ed2483137">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Find all countries that end in two vowels. 


```r
str_view_all_match(countries, "YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-9c1b31650b028762a2bf" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-9c1b31650b028762a2bf">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Find all countries that start with two non-vowels. How is this different from finding all countries that end in _at least_ two non-vowels? Hint: Syria.


```r
countries %>% 
    str_to_lower() %>% 
    str_view_all_match("YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-9b82a352bbdc6d6c847a" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-9b82a352bbdc6d6c847a">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Find all countries that have either "oo" or "cc" in them.


```r
str_view_all_match(countries, "YOUR_REGEX_HERE")
```

<!--html_preserve--><div id="htmlwidget-f9d9d315bc7aa09bedb7" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-f9d9d315bc7aa09bedb7">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
