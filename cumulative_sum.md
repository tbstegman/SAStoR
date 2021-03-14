
## SAS program to produce a cummulative sum by group

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept                               *
    ***********************************************;
    data data1;
      input group $ x;
      datalines;
      A 1
      A 2
      B 3
      B 4
      B 5
      C 6
      C 7
      C 8
      C 9
      ;

    ***************************************************
    *   Create a variable to store the cummulative    *
    *   sum by group                                  *
    ***************************************************;
    data data2;
        set data1;
            by group;
        if first.group then cummulative=x;
        else cummulative + x;
    run;

## R code to produce the same output

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c(rep('A',2),rep('B',3),rep('C',4)), 
                    x=1:9)
data1
```

    ##   group x
    ## 1     A 1
    ## 2     A 2
    ## 3     B 3
    ## 4     B 4
    ## 5     B 5
    ## 6     C 6
    ## 7     C 7
    ## 8     C 8
    ## 9     C 9

``` r
# Use the group_by and mutate verbs to produce the cummulative sum
data1 %>% 
  group_by(group) %>% 
  mutate(cumulative = cumsum(x)) %>% 
  ungroup()
```

    ## # A tibble: 9 x 3
    ##   group     x cumulative
    ##   <chr> <int>      <int>
    ## 1 A         1          1
    ## 2 A         2          3
    ## 3 B         3          3
    ## 4 B         4          7
    ## 5 B         5         12
    ## 6 C         6          6
    ## 7 C         7         13
    ## 8 C         8         21
    ## 9 C         9         30
