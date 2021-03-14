
## SAS program which duplicates rows

    ***********************************************
    *   Create a small data set to demonstrate    *
    *   the concept                               *
    ***********************************************;
    data data1;
        input group $ x;
        datalines;
        A 2
        B 3
        C 4
        ;

    ***********************************************
    *   Use the output statement to duplicate     *
    *   each record x times                       *
    ***********************************************;    
    data data2;
        set data1;
        do i = 1 to x;
            output;
        end;
        drop i;
    run;

## R code to produce the same output

``` r
# create the data frame that was used in the SAS program above
data1 <- data.frame(group=c('A','B','C'), x=c(2,3,4))
data1
```

    ##   group x
    ## 1     A 2
    ## 2     B 3
    ## 3     C 4

``` r
# First method using a for loop to create an index with repeated elements
idx <- c()
for (i in 1:nrow(data1)){
    idx <- c(idx,rep(i,data1$x[i]))
}
data1[idx,]
```

    ##     group x
    ## 1       A 2
    ## 1.1     A 2
    ## 2       B 3
    ## 2.1     B 3
    ## 2.2     B 3
    ## 3       C 4
    ## 3.1     C 4
    ## 3.2     C 4
    ## 3.3     C 4

``` r
# Second method using the sapply function
idx2 <- unlist(sapply(1:nrow(data1), function(i) rep(i,data1$x[i])))
data1[idx2,]
```

    ##     group x
    ## 1       A 2
    ## 1.1     A 2
    ## 2       B 3
    ## 2.1     B 3
    ## 2.2     B 3
    ## 3       C 4
    ## 3.1     C 4
    ## 3.2     C 4
    ## 3.3     C 4

``` r
# Third method using for loops and the rbind function
data2 <- data.frame()
for (i in 1:nrow(data1)){
  for (j in 1:data1$x[i]){
    data2 <- rbind(data2,data1[i,])
  }
}
data2
```

    ##    group x
    ## 1      A 2
    ## 2      A 2
    ## 21     B 3
    ## 22     B 3
    ## 23     B 3
    ## 3      C 4
    ## 31     C 4
    ## 32     C 4
    ## 33     C 4
