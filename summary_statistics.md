
## SAS program to calculate mutliple summary statistics per variable

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the use of proc means                     *
    ***********************************************;
    data data1;
      input category $ x y;
      datalines;
      A 1 10
      A 2 11
      B 3 12
      B 4 13
      B 5 14
      C 6 15
      C 7 16
      C 8 17
      C 9 18  
      ;

    **********************************************
    *   Compute the mean, min, and max of        *
    *   numeric variables by category.           *
    **********************************************;
    proc means data=data1 nway noprint;
        class category;
        var x y;
        output out=summary_stats(drop=_type_ _freq_) mean= min= max= / autoname;
    run;

## R code to produce the same summary

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c(rep('A',2),rep('B',3),rep('C',4)), 
                    x=1:9,
                    y=10:18)
data1
```

    ##   group x  y
    ## 1     A 1 10
    ## 2     A 2 11
    ## 3     B 3 12
    ## 4     B 4 13
    ## 5     B 5 14
    ## 6     C 6 15
    ## 7     C 7 16
    ## 8     C 8 17
    ## 9     C 9 18

``` r
# Utilize the group_by and summarise verbs to produce the summary
data1 %>% 
  group_by(group) %>% 
  summarise(across(where(is.numeric), list(mean = mean, min = min, max = max)))
```

    ## # A tibble: 3 x 7
    ##   group x_mean x_min x_max y_mean y_min y_max
    ##   <chr>  <dbl> <int> <int>  <dbl> <int> <int>
    ## 1 A        1.5     1     2   10.5    10    11
    ## 2 B        4       3     5   13      12    14
    ## 3 C        7.5     6     9   16.5    15    18
