---
layout: page
title: R for reproducible scientific analysis
subtitle: Vectors & Data frames
minutes: 50
---




> ## Learning objectives {.objectives}
>
> - Understand `data.frame` as common R object and familiar research data table.
>     - Columns = variables, rows = observations
>     - Columns = vectors
> - Be able to examine the structure and content of a data frame.
> - Understand vectors as fundamental R unit
> - Understand and be able to use vectorized operations
> - Be able to subset vectors with index and logical notation
> - Be able to import .RDA and .CSV files
>


### Loading data 

R has a variety functions to load specific kinds of data, e.g. `read.csv` to read a comma-separated value table. Two R-specific data-types are `.RDA` and `.RDS`. `.RDA` files, like the one you just saved, are created with the `save` function and accessed with the `load` function. Load needs the location of the saved file, provided as character string file-path, starting with the working directory. We won't get into working directories now, except to say that RStudio's projects makes this easier, by making the location of your project (where the `.Rproj` file is located) the default working directory. It is displayed at the top of your console pane in RStudio. File-paths should be provided relative to that location. So, to load the file we just saved:


~~~{.r}
load('data/continents.RDA')
~~~

### Intro to data.frames

Check your "Environment" tab -- a new object called `continents` has appeared. If your environment is in "list" mode, switch to "grid" as it's more informative. We can see that `continents` has Type data.frame and contains 6 observations of 4 variables. Click on the spreadsheet icon at far-right to view it. 

This is a `data.frame` -- the type of object most of us work with most of the time in R. In a `data.frame` each column represents a variable, and each row is an observation. This is the basic data organizational scheme -- one column per variable, one row per observation. You might recognize this form from from a statistics class or your own data analysis. 

#### Inspecting a data.frame

Rather than pulling up the spreadsheet form of a data.frame, we'd like to use R to get more information about it. In this case, our `data.frame` is so small that we can print the whole thing and inspect it:


~~~{.r}
continents
~~~



~~~{.output}
   continent area_km2 population percent_total_pop
1     Africa 30370000 1022234000              15.0
2   Americas 42330000  934611000              14.0
3 Antarctica 13720000       4490               0.0
4       Asia 43820000 4164252000              60.0
5     Europe 10180000  738199000              11.0
6    Oceania  9008500   29127000               0.4

~~~

When we start working with more-realistic datasets, let alone big data, that won't work. We can get the first few rows of a `data.frame` with `head` (in this case all the rows!). 


~~~{.r}
head(continents)
~~~



~~~{.output}
   continent area_km2 population percent_total_pop
1     Africa 30370000 1022234000              15.0
2   Americas 42330000  934611000              14.0
3 Antarctica 13720000       4490               0.0
4       Asia 43820000 4164252000              60.0
5     Europe 10180000  738199000              11.0
6    Oceania  9008500   29127000               0.4

~~~

`str` provides richer information about a `data.frame` and each element in it. It is generally a good first-step inspection of an R object.


~~~{.r}
str(continents)
~~~



~~~{.output}
'data.frame':	6 obs. of  4 variables:
 $ continent        : chr  "Africa" "Americas" "Antarctica" "Asia" ...
 $ area_km2         : num  30370000 42330000 13720000 43820000 10180000 ...
 $ population       : num  1.02e+09 9.35e+08 4.49e+03 4.16e+09 7.38e+08 ...
 $ percent_total_pop: num  15 14 0 60 11 0.4

~~~

We get some summary information on `continents`: it's type and dimensions, and we get some information on each variable in the `data.frame`. We'll get into the details of variable types later, but for now, note that `continent` is a character-type variable and the rest are numeric.

> #### Shoutout {.challenge}
>
> There is another function like `head()` and `str()` that provides information on a `data.frame`: `summary()`  
> - Call the `summary` function on the `continent` data.  
> - What is the average (mean) area of a continent?
>

### Vectors 

Vectors are a collection of observations of a single variable, ie a column in a data frame. They are the fundamental unit of R. Let's explore why that's useful and how to work with them.

We can extract a vector (ie a single variable) from a data frame with the `$` operator.


~~~{.r}
continents$population
~~~



~~~{.output}
[1] 1022234000  934611000       4490 4164252000  738199000   29127000

~~~

Note that you can use tab-completion to see what variables are available.

That just printed the six values of population. We are going to work with them some more, so extract them from the data.frame and store them to a new object, called `pop`:


~~~{.r}
pop <- continents$population
~~~

Now we have a new object in our environment: a numeric "vector" with six entries. R is built around vectors. In data analysis and statistics, we don't often work with individual numbers, but multiple observations. This is baked into R and helps it give it its power. 

Another core R concept is the idea that when you manipulate an object, the original object doesn't change. Note that the `continents` data.frame still has the population variable. R didn't "take it out" of `continents`; instead, it made a copy of it and stored it to a variable called `pop`. They are now two separate things. If we make a change to one, it will not affect the other. There is a powerful computational paradigm here that may not be apparent yet, but keep in mind that anything you do in R, if you want to keep the results, you need to assign them to a new object.

#### Vectorization

We just said that R is built around vectors. Most functions that accept a single value can also accept a vector of values. E.g. we can find the logarithm of each continent's population:


~~~{.r}
log(x = pop, base = 10)
~~~



~~~{.output}
[1] 9.009550 8.970631 3.652246 9.619537 8.868173 7.464296

~~~

R went over each item in `pop` and took the base-10 logarithm. Some functions take a vector but rather than returning a result for each item, operate on all of them together. E.g. we can find the mean and standard deviation of populations:


~~~{.r}
mean(pop)
~~~



~~~{.output}
[1] 1148071248

~~~



~~~{.r}
sd(pop)
~~~



~~~{.output}
[1] 1542519717

~~~

Many functions will also operate element-wise over multiple vectors. E.g. to calculate the population density of each continent, we need to divide the population by the land area for each continent. We can do that with a single command:


~~~{.r}
continents$population / continents$area_km2
~~~



~~~{.output}
[1] 3.365933e+01 2.207916e+01 3.272595e-04 9.503085e+01 7.251464e+01
[6] 3.233280e+00

~~~

Note that we didn't have to take those vectors out of the data.frame to use them. We can do vectorized operations right in the data.frame, which helps keep everything organized: recall that each row of a data.frame is a particular observation (in this case a continent), so we often want to do operations with each row.

Just like we can extract a column from a data.frame with `$`, we can make a new column:


~~~{.r}
continents$pop_density <- continents$population / continents$area_km2
continents
~~~



~~~{.output}
   continent area_km2 population percent_total_pop  pop_density
1     Africa 30370000 1022234000              15.0 3.365933e+01
2   Americas 42330000  934611000              14.0 2.207916e+01
3 Antarctica 13720000       4490               0.0 3.272595e-04
4       Asia 43820000 4164252000              60.0 9.503085e+01
5     Europe 10180000  738199000              11.0 7.251464e+01
6    Oceania  9008500   29127000               0.4 3.233280e+00

~~~

### Creating vectors

We can also manually create new vectors. There are many ways to do this, but the most flexible is the combine function, `c()`.


~~~{.r}
c(1, 3, 5)
~~~



~~~{.output}
[1] 1 3 5

~~~

Let's store that vector in a new variable. We will use it again soon.


~~~{.r}
odds <- c(1, 3, 5)
~~~


### Subsetting

#### Subset by index

We can extract items from a vector by specifying which positions, or indices, we want. R's syntax for subsetting is square brackets (`[ ]`) at the end of an object containing the positions to return. So to get the third element out of our `pop` vector:


~~~{.r}
pop
~~~



~~~{.output}
[1] 1022234000  934611000       4490 4164252000  738199000   29127000

~~~



~~~{.r}
pop[3]
~~~



~~~{.output}
[1] 4490

~~~

To get the first three elements, we need to put a vector containing 1, 2, and 3 inside the `[ ]`. We just learned how to make such a vector using the combine function.


~~~{.r}
pop[c(1, 2, 3)]
~~~



~~~{.output}
[1] 1022234000  934611000       4490

~~~

Sometimes it will be more useful to provide the desired positions as a variable. Let's pull out the odd-positioned entries from `pop`:


~~~{.r}
pop[odds]
~~~



~~~{.output}
[1] 1022234000       4490  738199000

~~~

We can also tell R which elements we *don't* want with a `-`. This returns each element in `pop` except the first one:


~~~{.r}
pop[-1]
~~~



~~~{.output}
[1]  934611000       4490 4164252000  738199000   29127000

~~~

If we try to ask for an element that doesn't exist, R returns `NA`. `NA` is a special value in R that means "missing".


~~~{.r}
pop[10]
~~~



~~~{.output}
[1] NA

~~~


> #### Challenge -- Create and subset a vector {.challenge}
>
> Similar to `c`, the `seq` function creates a vector: a **seq**uence of numbers. 
>
> - Your first task is to create a sequence of all the multiples of three from three to 300. Figure out how to do this. Some combination of playing with the function in the console and reading its helpfile (`?seq`) should work. Helpfiles are challenging at first, but it's important to learn how to find the info you need in them. Hint: The arguments you need here are `from`, `to`, and `by`.
>
> - Store your sequence in a variable.
> - Extract the 33rd entry from the sequence
> - **Bonus**: Create a vector with ten evenly-spaced numbers starting with one and ending with one-million. What is the sum of the second and ninth entries in this vector?
> - **Super-Bonus**: Returning to the multiples-of-three vector, what is the sum of the numbers in positions that are not evenly divisible by three? That is, the sum of the first, second, fourth, fifth, seventh, etc. entries.
>

#### Subset by logical

Note that the `continents` data frame contains two common kinds of variables: numbers (num) and strings (chr).


~~~{.r}
str(continents)
~~~



~~~{.output}
'data.frame':	6 obs. of  5 variables:
 $ continent        : chr  "Africa" "Americas" "Antarctica" "Asia" ...
 $ area_km2         : num  30370000 42330000 13720000 43820000 10180000 ...
 $ population       : num  1.02e+09 9.35e+08 4.49e+03 4.16e+09 7.38e+08 ...
 $ percent_total_pop: num  15 14 0 60 11 0.4
 $ pop_density      : num  3.37e+01 2.21e+01 3.27e-04 9.50e+01 7.25e+01 ...

~~~

A third important data type in R is logicals. You saw this when making logical comparisons like `1 > 0`. Logical data can only be `TRUE` or `FALSE` (or `NA` for missing; any variable type in R can have `NA`-missing values). 

Logical comparisons are vectorized like other things in R. Let's find the highly populated continents, those with more than one-billion people. One-billion is 10^9, so we can write it as `1e9`.


~~~{.r}
pop > 1e9
~~~



~~~{.output}
[1]  TRUE FALSE FALSE  TRUE FALSE FALSE

~~~

That went over each element in `pop` and compared it with 10. We say that R "recycled" 10 to compare it with each element in `pop`. 

In the same way that we subset by index before, we can subset by a logical vector. To find the values of `pop` that were greater than one-billion, we subset it like so:


~~~{.r}
pop[pop > 1e9]
~~~



~~~{.output}
[1] 1022234000 4164252000

~~~

> #### Shoutout {.challenge}
>
> How could you extract the names of the continents with more than one-billion people?
>

Recall that in a data frame, each row constitutes a single observation. This makes it especially useful to test one column and use it to subset another -- we often want the entries of some variable where a condition on another variable is met. For example, to find the land-area of Africa, we can test the continent names for being "Africa" and extract the area where that is true. 

Here as elsewhere, it is often useful to build code from the insight out, ie, write the logical test first, then go left and write what you want to subset with it.


~~~{.r}
continents$area_km2[continents$continent == "Africa"]
~~~



~~~{.output}
[1] 30370000

~~~


> #### MCQ -- Subset and vectorize {.challenge}
>
> What is the total land area of continents that have at least 10% of the world's population? 
>
> - Use subsetting to get the areas you want
> - Use the `sum` function to find the total land area
>
> a. 149428500
> b. 126700000
> c. 22728500
> d. 100
>




### Reading csv data

The `continents` data.frame was useful for learning because it was so small, but it's time to graduate to something more interesting and realistic. Data come in many forms, and we need to be able to load them in R. For our own use and with others who use R, there are R-specific data structures we can use, like the .RDA file-type we just saw, but we need to be able to work with more general data types too. Comma-separated value (csv) tables are perhaps the most universal data structure. 

The gapminder dataset provides country-by-year level data on gdp, population, and longevity. I downloaded it and put it in the data directory of my project. You will do the same in a minute.

We can read csv's with the `read.csv` function. The argument to `read.csv` is the location of the file, relative to your working directory. Since I saved the gapminder data to the `data` directory of my project, I can load it with this. Note the use of tab-completion to find the file and get it exactly right without typos. 


~~~{.r}
read.csv('data/gapminder-FiveYearData.csv')
~~~

Whoa! What just happened? R executed the function and printed the result, just like when you enter `log(1)`. How do you store an object to a variable?


~~~{.r}
gapminder <- read.csv('data/gapminder-FiveYearData.csv')
~~~

Now, a data.frame called `gapminder` is in my Environment, and I can see that it is a data.frame with 1704 rows and 6 columns. 

> #### Challenge -- read csv data {.challenge}
>
> The gapminder data are available at [this link](https://raw.githubusercontent.com/resbaz/r-novice-gapminder-files/master/data/gapminder-FiveYearData.csv).  
> - Right click on the link to "save file as..."  
> - Save the .csv file in the `/data` directory of your project.  
> - Read the data with the `read.csv` function and assign it to the variable `gapminder`.  
> - Inspect the data.frame using the `summary` function. What is the most recent year for which we have data? 
> 
> **Advanced challenge**
>
> Suppose you get a .csv file from a colleague in Europe. Because they use "," (comma) as a decimal separator, they use ";" (semi-colons) to separate fields. How can you read it into R? 
>
> Feel free to use the web and/or R's helpfiles.    
>

