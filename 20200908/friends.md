Friends
================
James P. Hare
9/8/2020

``` r
friends <-
        readr::read_csv(
                'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-08/friends.csv',
                col_types = cols(
                        text = col_character(),
                        speaker = col_character(),
                        season = col_double(),
                        episode = col_double(),
                        scene = col_double(),
                        utterance = col_double()
                )
        )
friends_emotions <-
        readr::read_csv(
                'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-08/friends_emotions.csv',
                col_types = cols(
                        season = col_integer(),
                        episode = col_integer(),
                        scene = col_integer(),
                        utterance = col_integer(),
                        emotion = col_character()
                )
        )
friends_info <-
        readr::read_csv(
                'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-08/friends_info.csv',
                col_types = cols(
                        season = col_integer(),
                        episode = col_integer(),
                        title = col_character(),
                        directed_by = col_character(),
                        written_by = col_character(),
                        air_date = col_date(format = ""),
                        us_views_millions = col_double(),
                        imdb_rating = col_double()
                )
        )
```
