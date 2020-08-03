European Energy
================
James Hare
8/3/2020

## European Energy

``` r
energy_types_tidy <- energy_types %>% 
         pivot_longer(names_to = "year", 
               values_to = "gwh", 
               cols = "2016":"2018",
               names_transform = list(year = as.integer)) 
```

![](European-Energy_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
