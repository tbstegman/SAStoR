
## SAS program to concatenate a variable across rows

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept                               *
    ***********************************************;
    data data1;
      input category $ y $;
      datalines;
      A y1
      A y2
      B y3
      B y4
      B y5
      C y6
      C y7
      C y8
      C y9 
      ;

    ***********************************************
    *   Combine the y variable values across      *
    *   rows by category                          *
    ***********************************************;
    data data2;
        set data1;
            by category;
        length combined $ 250;
        retain combined;
        if first.category then combined=y;
        else combined=catx(',',combined,y);
        if last.category;
        drop y;
    run;

## R code to produce the same output

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(category=c(rep('A',2),rep('B',3),rep('C',4)), 
                    y=c('y1','y2','y3','y4','y5','y6','y7','y8','y9'))
data1
```

    ##   category  y
    ## 1        A y1
    ## 2        A y2
    ## 3        B y3
    ## 4        B y4
    ## 5        B y5
    ## 6        C y6
    ## 7        C y7
    ## 8        C y8
    ## 9        C y9

``` r
# Concatenate y by group across rows using summarise with paste
data1 %>% 
  group_by(category) %>% 
  summarise(combined = paste(y,collapse = ','))
```

    ## # A tibble: 3 x 2
    ##   category combined   
    ##   <chr>    <chr>      
    ## 1 A        y1,y2      
    ## 2 B        y3,y4,y5   
    ## 3 C        y6,y7,y8,y9
