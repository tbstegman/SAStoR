
### SAS program to fill in gaps in a time series by group

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept                               *
    ***********************************************;
    data data1;
        input group $ date monyy7. x;
        format date monyy7.;
        datalines;
    A Jan2020 1
    A Mar2020 2
    B Feb2020 3
    B Jun2020 4
    B Aug2020 5
    C Apr2020 6
    ;

    ***********************************************
    ***********************************************
    *   Here is a method to fill in the gaps in   *
    *   the time series by group using base SAS   *
    ***********************************************
    ***********************************************;

    ***********************************************
    *   Find the min and max months by group      *
    ***********************************************;
    proc means data=data1 nway noprint;
        class group;
        var date;
        output out=min_max_dates min=min_date max=max_date;
    run;

    ***********************************************
    *  Create a data set template with all months *
    *  from the min to the max for each group     *
    ***********************************************;
    data min_max_dates2;
        set min_max_dates;
        n=intck('month',min_date,max_date);
        do i = 0 to n;
            date = intnx('month',min_date,i);
            output;
        end;
        format date monyy7.;
        keep group date;
    run;

    ***********************************************
    *   Merge the original data set with the      *
    *   template and forward fill the x variable  *
    ***********************************************;
    data data2;
        merge min_max_dates2 data1;
            by group date;
        retain ffill;
        if x ne . then ffill=x;
        drop x;
        rename ffill=x;
    run;

    ***********************************************
    ***********************************************
    *  If the ETS module is available, this task  *
    *  can easily be done using proc timeseries   *
    ***********************************************
    ***********************************************;
    proc timeseries data=data1 out=data3;
        by group;
        id date interval=month setmissing=previous;
        var x;
    run;

## R code to produce the same output

``` r
# Load the tidyverse and padr packages
library(tidyverse)
library(padr)

# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c(rep('A',2),rep('B',3),'C'), 
                    date=as.Date(c('2020-01-01','2020-03-01','2020-02-01',
                                   '2020-06-01','2020-08-01','2020-04-01')),
                    x=1:6)
data1
```

    ##   group       date x
    ## 1     A 2020-01-01 1
    ## 2     A 2020-03-01 2
    ## 3     B 2020-02-01 3
    ## 4     B 2020-06-01 4
    ## 5     B 2020-08-01 5
    ## 6     C 2020-04-01 6

``` r
# Forward fill in gaps in the series for each group
data1 %>% 
  group_by(group) %>% 
  pad(interval='month') %>% 
  fill(x,.direction = "down")
```

    ## Warning: datetime variable does not vary for 1 of the groups, no padding applied
    ## on this / these group(s)

    ## # A tibble: 11 x 3
    ## # Groups:   group [3]
    ##    group date           x
    ##    <chr> <date>     <int>
    ##  1 A     2020-01-01     1
    ##  2 A     2020-02-01     1
    ##  3 A     2020-03-01     2
    ##  4 B     2020-02-01     3
    ##  5 B     2020-03-01     3
    ##  6 B     2020-04-01     3
    ##  7 B     2020-05-01     3
    ##  8 B     2020-06-01     4
    ##  9 B     2020-07-01     4
    ## 10 B     2020-08-01     5
    ## 11 C     2020-04-01     6
