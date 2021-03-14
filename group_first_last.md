
## SAS program to flag the first and last observations by group

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept.                              *
    ***********************************************;
    data data1;
        input group $ @@;
        datalines;
    A A B B B C C C C
    ;

    ***********************************************
    *   Flag the first and last observations      *
    *   by group                                  *
    ***********************************************;
    data data2;
        set data1;
            by group;
        first = first.group;
        last = last.group;
    run;

## R code to produce the same output

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c(rep("A",2),rep("B",3),rep("C",4)))
data1
```

    ##   group
    ## 1     A
    ## 2     A
    ## 3     B
    ## 4     B
    ## 5     B
    ## 6     C
    ## 7     C
    ## 8     C
    ## 9     C

``` r
# Utilize the group_by and mutate verbs to produce the desired output
data1 %>% 
  group_by(group) %>% 
  mutate(first = as.numeric(row_number()==min(row_number())),
         last = as.numeric(row_number()==max(row_number()))) %>% 
  ungroup()
```

    ## # A tibble: 9 x 3
    ##   group first  last
    ##   <chr> <dbl> <dbl>
    ## 1 A         1     0
    ## 2 A         0     1
    ## 3 B         1     0
    ## 4 B         0     0
    ## 5 B         0     1
    ## 6 C         1     0
    ## 7 C         0     0
    ## 8 C         0     0
    ## 9 C         0     1
