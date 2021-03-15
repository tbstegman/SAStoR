
## SAS program to transpose data sets

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the use of proc transpose                 *
    ***********************************************;
    data data1;
      input category $ x y $;
      datalines;
      A 1 y1
      A 2 y2
      B 1 y3
      B 2 y4
      B 3 y5
      C 1 y6
      C 2 y7
      C 3 y8
      C 4 y9 
      ;
      
    ***********************************************
    *   Transpose the data to wider format        *
    ***********************************************;
    proc transpose data=data1 out=data2(drop=_name_) prefix=col;
        by category;
        id x;
        var y;
    run;


    ***********************************************
    *   Transpose the data back to longer format  *
    ***********************************************;
    proc transpose data=data2 out=data3;
        by category;
        var col:;
    run;

    proc sql;
        create table data4 as
        select category,
               input(substr(_name_,4),8.) as x,
               col1 as y
        from data3
        where col1 ne "";
    quit;

## R code to produce the same output

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c(rep('A',2),rep('B',3),rep('C',4)), 
                    x=c(1:2,1:3,1:4),
                    y=c('y1','y2','y3','y4','y5','y6','y7','y8','y9'))
data1
```

    ##   group x  y
    ## 1     A 1 y1
    ## 2     A 2 y2
    ## 3     B 1 y3
    ## 4     B 2 y4
    ## 5     B 3 y5
    ## 6     C 1 y6
    ## 7     C 2 y7
    ## 8     C 3 y8
    ## 9     C 4 y9

``` r
# Transpose the data to wider format
data2 <- data1 %>% pivot_wider(names_from=x,
                               values_from=y,
                               names_prefix='col')
data2
```

    ## # A tibble: 3 x 5
    ##   group col1  col2  col3  col4 
    ##   <chr> <chr> <chr> <chr> <chr>
    ## 1 A     y1    y2    <NA>  <NA> 
    ## 2 B     y3    y4    y5    <NA> 
    ## 3 C     y6    y7    y8    y9

``` r
# Transpose the data back to longer format
data2 %>% pivot_longer(cols=!group,
                       names_to="x", 
                       values_to="y",
                       values_drop_na=TRUE) %>% 
  mutate(x = as.numeric(gsub("[a-zA-Z ]", "", x)))
```

    ## # A tibble: 9 x 3
    ##   group     x y    
    ##   <chr> <dbl> <chr>
    ## 1 A         1 y1   
    ## 2 A         2 y2   
    ## 3 B         1 y3   
    ## 4 B         2 y4   
    ## 5 B         3 y5   
    ## 6 C         1 y6   
    ## 7 C         2 y7   
    ## 8 C         3 y8   
    ## 9 C         4 y9
