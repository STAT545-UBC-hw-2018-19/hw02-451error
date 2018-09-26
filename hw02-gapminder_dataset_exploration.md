hm02-gapminder exploration
================

In this Rmd file, I will be exploring the `gapminder` dataset using
`ggplot2`. To get started, I will be loading the `tidyverse` package and
the `gapminder`
    dataset.

``` r
library(tidyverse)
```

    ## -- Attaching packages ----------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.0.0     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.6
    ## v tidyr   0.8.1     v stringr 1.3.1
    ## v readr   1.1.1     v forcats 0.3.0

    ## -- Conflicts -------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(gapminder)
```

## Smell test the data

### The `gapminder` dataset

The `gapminder` dataset contains data on life expectancy, GDP per capita
and population by country. We can see the head of the dataset:

``` r
head(gapminder)
```

    ## # A tibble: 6 x 6
    ##   country     continent  year lifeExp      pop gdpPercap
    ##   <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ## 1 Afghanistan Asia       1952    28.8  8425333      779.
    ## 2 Afghanistan Asia       1957    30.3  9240934      821.
    ## 3 Afghanistan Asia       1962    32.0 10267083      853.
    ## 4 Afghanistan Asia       1967    34.0 11537966      836.
    ## 5 Afghanistan Asia       1972    36.1 13079460      740.
    ## 6 Afghanistan Asia       1977    38.4 14880372      786.

We can check that `gapminder` is dataframe:

``` r
inherits(gapminder,'data.frame')
```

    ## [1] TRUE

As we can see below, `gapminder` is a dataframe which has has 1704 rows
and 6 columns.

``` r
typeof(gapminder)
```

    ## [1] "list"

``` r
nrow(gapminder)
```

    ## [1] 1704

``` r
ncol(gapminder)
```

    ## [1] 6

We can also use the `dim` function to get the dimensions of the
dataframe:

``` r
dim(gapminder)
```

    ## [1] 1704    6

From the below, we can see that two of the columns are categorical
variables and the remaining four are continuous random variables.

``` r
summary(gapminder)
```

    ##         country        continent        year         lifeExp     
    ##  Afghanistan:  12   Africa  :624   Min.   :1952   Min.   :23.60  
    ##  Albania    :  12   Americas:300   1st Qu.:1966   1st Qu.:48.20  
    ##  Algeria    :  12   Asia    :396   Median :1980   Median :60.71  
    ##  Angola     :  12   Europe  :360   Mean   :1980   Mean   :59.47  
    ##  Argentina  :  12   Oceania : 24   3rd Qu.:1993   3rd Qu.:70.85  
    ##  Australia  :  12                  Max.   :2007   Max.   :82.60  
    ##  (Other)    :1632                                                
    ##       pop              gdpPercap       
    ##  Min.   :6.001e+04   Min.   :   241.2  
    ##  1st Qu.:2.794e+06   1st Qu.:  1202.1  
    ##  Median :7.024e+06   Median :  3531.8  
    ##  Mean   :2.960e+07   Mean   :  7215.3  
    ##  3rd Qu.:1.959e+07   3rd Qu.:  9325.5  
    ##  Max.   :1.319e+09   Max.   :113523.1  
    ## 

We can use the `sapply` function to check the type and class of each of
the columns of the `gapminder` dataframe:

``` r
sapply(gapminder,typeof)
```

    ##   country continent      year   lifeExp       pop gdpPercap 
    ## "integer" "integer" "integer"  "double" "integer"  "double"

``` r
sapply(gapminder,class)
```

    ##   country continent      year   lifeExp       pop gdpPercap 
    ##  "factor"  "factor" "integer" "numeric" "integer" "numeric"

In particular, notice that the summary shows that `year` is being
treated as a continuous variable. We can make `R` to treat it as a
categorical variable thus:

``` r
gapminder$year = as.factor(gapminder$year)
class(gapminder$year)
```

    ## [1] "factor"

``` r
summary(gapminder$year)
```

    ## 1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 2002 2007 
    ##  142  142  142  142  142  142  142  142  142  142  142  142

## Explore individual variables

### Categorical variable

We can have a look at the countries categorical variable. We can select
the countries column from `gapminder` using `select`:

``` r
select(gapminder,country)
```

    ## # A tibble: 1,704 x 1
    ##    country    
    ##    <fct>      
    ##  1 Afghanistan
    ##  2 Afghanistan
    ##  3 Afghanistan
    ##  4 Afghanistan
    ##  5 Afghanistan
    ##  6 Afghanistan
    ##  7 Afghanistan
    ##  8 Afghanistan
    ##  9 Afghanistan
    ## 10 Afghanistan
    ## # ... with 1,694 more rows

This has a lot of replicates due to multiple years’ worth of data. We
can remove the duplicates using the `unique` function in `R`:

``` r
unique(select(gapminder,country))
```

    ## # A tibble: 142 x 1
    ##    country    
    ##    <fct>      
    ##  1 Afghanistan
    ##  2 Albania    
    ##  3 Algeria    
    ##  4 Angola     
    ##  5 Argentina  
    ##  6 Australia  
    ##  7 Austria    
    ##  8 Bahrain    
    ##  9 Bangladesh 
    ## 10 Belgium    
    ## # ... with 132 more rows

Then we can find the total number of countries in the data set using the
`count` function:

``` r
gapminder %>% 
  select(country) %>% 
  unique() %>% 
  count()
```

    ## # A tibble: 1 x 1
    ##       n
    ##   <int>
    ## 1   142

We can find the number of times each country has appeared on this
dataset as follows (only first 6 shown):

``` r
country_count = table(gapminder$country)
head(country_count)
```

    ## 
    ## Afghanistan     Albania     Algeria      Angola   Argentina   Australia 
    ##          12          12          12          12          12          12

We can also look at how many countries (in this dataset) are in each
continent:

``` r
gapminder %>% 
  select(country,continent) %>% 
  unique() %>% 
  ggplot(aes(x=continent)) +
  geom_bar(aes(fill=continent)) +
  ggtitle('Countries Per Continent')
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/countries%20bar%20plot-1.png)<!-- -->

### Quantitative variable

Let’s take a look at the GDP per capita data. Here’s a quick summary of
the data:

``` r
gapminder %>% 
  select(gdpPercap) %>% 
  summary()
```

    ##    gdpPercap       
    ##  Min.   :   241.2  
    ##  1st Qu.:  1202.1  
    ##  Median :  3531.8  
    ##  Mean   :  7215.3  
    ##  3rd Qu.:  9325.5  
    ##  Max.   :113523.1

We can see this on a basic box plot. From the summary, we can see the
average and median are very log compared to the maximum, so we will use
a log scale in the box plot:

``` r
ggplot(gapminder,aes(y=gdpPercap)) + 
  scale_y_log10() +
  geom_boxplot()
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/gdp%20boxplot-1.png)<!-- -->

We can also look at the GDP based on continent:

``` r
ggplot(gapminder,aes(x=continent,y=gdpPercap)) + 
  scale_y_log10() +
  geom_boxplot()
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/gdp%20boxplot%20continent-1.png)<!-- -->

We can also see the spread of the data from a histogram. Similar to
before, we used a log scale, but this time for the x axis.

``` r
ggplot(gapminder,aes(gdpPercap)) +
  geom_histogram(bins=50) +
  scale_x_log10()
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/gdp%20histogram-1.png)<!-- -->

We can combine the histogram and a density plot:

``` r
ggplot(gapminder,aes(gdpPercap)) +
  scale_x_log10() +
  geom_histogram(aes(y=..density..),alpha=0.5) +
  geom_density()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](hw02-gapminder_dataset_exploration_files/figure-gfm/gdp%20histogram%20density-1.png)<!-- -->

## Explore various plot types

Let’s compare GDP per capita against population on a scatter plot:

``` r
ggplot(gapminder,aes(pop,gdpPercap)) +
  scale_x_log10() +
  scale_y_log10() +
  geom_point(alpha=0.3)
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/pop%20vs%20gdp-1.png)<!-- -->

We can see the same plot but with different continents reflected as
different colours:

``` r
ggplot(gapminder,aes(pop,gdpPercap)) +
  scale_x_log10() +
  scale_y_log10() +
  geom_point(aes(colour=continent),alpha=0.4)
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/pop%20vs%20gdp%20colour-1.png)<!-- -->

We can also use colours to reflect which points has a high life
expectancy:

``` r
ggplot(gapminder,aes(pop,gdpPercap)) +
  scale_x_log10() +
  scale_y_log10() +
  geom_point(aes(colour=lifeExp>65),alpha=0.4)
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/pop%20vs%20gdp%20with%20life%20exp-1.png)<!-- -->

Lastly, we can track how a certain country’s GDP and population changed
on the above plot. For this illustation, I decided to choose Japan I
used the `last_plot()` command to pull up the previous plot.

``` r
last_plot() + 
  geom_path(data=filter(gapminder,country=='Japan'),arrow=arrow())
```

![](hw02-gapminder_dataset_exploration_files/figure-gfm/pop%20vs%20gdp%20with%20path-1.png)<!-- -->
