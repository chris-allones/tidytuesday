European Energy
================
James Hare
8/3/2020

``` r
knitr::opts_chunk$set(echo = TRUE)

#Load libraries

library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ─────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(sf)
```

    ## Linking to GEOS 3.7.2, GDAL 2.4.2, PROJ 5.2.0

``` r
library(rnaturalearth)
library(rnaturalearthdata)

#Load map data from Natural Earth

world <- ne_countries(scale = "medium", returnclass = "sf")

#Load this week's Tidy Tuesday data

country_totals <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-08-04/energy_types.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   country = col_character(),
    ##   country_name = col_character(),
    ##   type = col_character(),
    ##   level = col_character(),
    ##   `2016` = col_double(),
    ##   `2017` = col_double(),
    ##   `2018` = col_double()
    ## )

``` r
#Make countries match in map data and energy data

world <- world %>% 
        rename(country_name = geounit)

country_totals <- country_totals %>% 
        mutate(country_name = case_when(country_name == "North Macedonia" ~ "Macedonia",
                              country_name == "Serbia" ~ "Republic of Serbia",
                              country_name == "Bosnia & Herzegovina" ~ "Bosnia and Herzegovina",
                              country_name == "Czechia" ~ "Czech Republic",
                                  is.na(country_name) ~ "United Kingdom",
                                  TRUE ~ country_name))


#Tidy the data

country_totals_tidy <- country_totals %>% 
         pivot_longer(names_to = "year", 
               values_to = "gwh", 
               cols = "2016":"2018",
               names_transform = list(year = as.integer)) %>%
        group_by(country, country_name, type) %>%
        summarise(gwh = sum(gwh)) %>% 
        
        #calculate percentage of modern renewables (wind and solar)
        
        pivot_wider(names_from = type,
                    values_from = gwh) %>% 
        mutate(total = sum(`Conventional thermal`, Geothermal, Hydro, Nuclear,
                           Other, `Pumped hydro power`, Solar, Wind),
               modern = sum(Solar, Wind),
               pct_modern = (modern / total) * 100) %>% 
        
        #add map data
        left_join(world)
```

    ## `summarise()` regrouping output by 'country', 'country_name' (override with `.groups` argument)

    ## Joining, by = "country_name"

``` r
#plot the data

ggplot(country_totals_tidy) +
        geom_sf(aes(fill = pct_modern, geometry = geometry)) +
         coord_sf(xlim = c(-15, 50), ylim = c(35, 72)) +
        labs(title = "Uneven Adoption of Modern Renewables in Europe",
             subtitle = "Net Wind and Solar Electricity Generation Varies Greatly",
             caption = "Source: Eurostat") +
        theme(legend.position = c(1, 1),
              legend.justification = c(1.2, 1),
              axis.text.x = element_blank(),
              axis.ticks.x = element_blank(),
              axis.text.y = element_blank(),
              axis.ticks.y = element_blank(),
              panel.grid.major = element_blank(), 
              panel.grid.minor = element_blank(),
              panel.background = element_blank()) +
        scale_fill_viridis_b(name = "Wind and Solar\nas Percentage of\nNet Production,\n2016 - 2018")
```

![](European-Energy_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
