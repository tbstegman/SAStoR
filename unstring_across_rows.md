
## SAS program to expand strings across rows

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept                               *
    ***********************************************;
    data data1;
        input group $ x $10.;
        datalines;
        A a1
        B b1,b2
        C c1,c2,c3
        ;
        
    ***********************************************
    *   Create a new row for each part of the     *
    *   string for each value of x                *
    ***********************************************;
    data data2;
        set data1;
        commas = compress(x,',','k');
        if commas = "" then n=1;
        else n= length(commas) + 1;
        do i = 1 to n;
            part = scan(x,i);
            output;
        end;
        keep group part;
    run;

## R code to produce the same output

``` r
# Load the tidyverse
library(tidyverse)

# create the data frame that was used in the SAS program above
data1 <- data.frame(category=c('A','B','C'), 
                    y=c('a1','b1,b2','c1,c2,c3'))
data1
```

    ##   category        y
    ## 1        A       a1
    ## 2        B    b1,b2
    ## 3        C c1,c2,c3

``` r
# Calculate the number of columns that will be needed to separate all strings
n <- max(str_count(data1$y,",")) + 1

# Use separate to expand the strings across columns, and then transpose
data1 %>% 
  separate(y,as.character(1:n),fill='right') %>% 
  pivot_longer(cols=!category,values_to="part",values_drop_na=TRUE) %>% 
  select(category,part)
```

    ## # A tibble: 6 x 2
    ##   category part 
    ##   <chr>    <chr>
    ## 1 A        a1   
    ## 2 B        b1   
    ## 3 B        b2   
    ## 4 C        c1   
    ## 5 C        c2   
    ## 6 C        c3
