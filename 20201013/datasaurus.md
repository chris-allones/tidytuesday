Datasaurus
================
James P. Hare
10/13/2020

``` r
knitr::opts_chunk$set(echo = TRUE)

library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(broom)

theme_set(theme_minimal())

datasaurus <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-10-13/datasaurus.csv')
```

    ## 
    ## ── Column specification ──────────────────────────────────────────────────────────────────────────────────────────────────────────────
    ## cols(
    ##   dataset = col_character(),
    ##   x = col_double(),
    ##   y = col_double()
    ## )

``` r
glimpse(datasaurus)
```

    ## Rows: 1,846
    ## Columns: 3
    ## $ dataset <chr> "dino", "dino", "dino", "dino", "dino", "dino", "dino", "dino…
    ## $ x       <dbl> 55.3846, 51.5385, 46.1538, 42.8205, 40.7692, 38.7179, 35.6410…
    ## $ y       <dbl> 97.1795, 96.0256, 94.4872, 91.4103, 88.3333, 84.8718, 79.8718…

``` r
summary(datasaurus)
```

    ##    dataset                x               y           
    ##  Length:1846        Min.   :15.56   Min.   : 0.01512  
    ##  Class :character   1st Qu.:41.07   1st Qu.:22.56107  
    ##  Mode  :character   Median :52.59   Median :47.59445  
    ##                     Mean   :54.27   Mean   :47.83510  
    ##                     3rd Qu.:67.28   3rd Qu.:71.81078  
    ##                     Max.   :98.29   Max.   :99.69468

``` r
datasaurus %>%
        group_by(dataset) %>% 
        summarize(Min_X = min(x),
                  Mean_X = mean(x),
                  Mean_Y = mean(y),
                  SD_X = sd(x),
                  SD_Y = sd(y),
                  Corr = cor(x, y)
                  )
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 13 x 7
    ##    dataset    Min_X Mean_X Mean_Y  SD_X  SD_Y    Corr
    ##    <chr>      <dbl>  <dbl>  <dbl> <dbl> <dbl>   <dbl>
    ##  1 away        15.6   54.3   47.8  16.8  26.9 -0.0641
    ##  2 bullseye    19.3   54.3   47.8  16.8  26.9 -0.0686
    ##  3 circle      21.9   54.3   47.8  16.8  26.9 -0.0683
    ##  4 dino        22.3   54.3   47.8  16.8  26.9 -0.0645
    ##  5 dots        25.4   54.3   47.8  16.8  26.9 -0.0603
    ##  6 h_lines     22.0   54.3   47.8  16.8  26.9 -0.0617
    ##  7 high_lines  17.9   54.3   47.8  16.8  26.9 -0.0685
    ##  8 slant_down  18.1   54.3   47.8  16.8  26.9 -0.0690
    ##  9 slant_up    20.2   54.3   47.8  16.8  26.9 -0.0686
    ## 10 star        27.0   54.3   47.8  16.8  26.9 -0.0630
    ## 11 v_lines     30.4   54.3   47.8  16.8  26.9 -0.0694
    ## 12 wide_lines  27.4   54.3   47.8  16.8  26.9 -0.0666
    ## 13 x_shape     31.1   54.3   47.8  16.8  26.9 -0.0656

``` r
datasaurus %>%
        nest(data = -dataset) %>% 
        mutate(model = map(data, ~ lm(y ~ x, data = .)),
               tidied = map(model, tidy)
        ) %>% 
        unnest(tidied) %>% 
        select(-data, -model)
```

    ## # A tibble: 26 x 6
    ##    dataset term        estimate std.error statistic  p.value
    ##    <chr>   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
    ##  1 dino    (Intercept)  53.5        7.69      6.95  1.29e-10
    ##  2 dino    x            -0.104      0.136    -0.764 4.46e- 1
    ##  3 away    (Intercept)  53.4        7.69      6.94  1.31e-10
    ##  4 away    x            -0.103      0.135    -0.760 4.48e- 1
    ##  5 h_lines (Intercept)  53.2        7.70      6.91  1.53e-10
    ##  6 h_lines x            -0.0992     0.136    -0.732 4.66e- 1
    ##  7 v_lines (Intercept)  53.9        7.69      7.01  9.38e-11
    ##  8 v_lines x            -0.112      0.135    -0.824 4.12e- 1
    ##  9 x_shape (Intercept)  53.6        7.69      6.97  1.17e-10
    ## 10 x_shape x            -0.105      0.135    -0.778 4.38e- 1
    ## # … with 16 more rows

``` r
datasaurus %>% 
        ggplot(aes(x, y, color = dataset)) +
        geom_point() +
        geom_smooth(method = "lm", formula = y ~ x)
```

![](datasaurus_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
datasaurus %>% 
        ggplot(aes(x, y, color = dataset)) +
        geom_point() +
        facet_wrap(~ dataset)
```

![](datasaurus_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
