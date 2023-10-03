Viz Part 2
================
Lu Qiu
2023-09-28

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggridges)
library(patchwork)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: C:\Users\Surface\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:21:41.900812 (8.541)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: C:\Users\Surface\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:22:37.330137 (3.838)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: C:\Users\Surface\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:22:50.115639 (0.996)

    ## file min/max dates: 1999-09-01 / 2023-09-30

This result is a dataframe with 2190 observations and 6 variables.

### Same plot from last time

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = 'Min daily temp (Degree C)',
    y = 'Max daily temp',
    color = 'Location',
    caption = 'Max vs min daily temp in three locations; data from rnoaa'
  ) +
  scale_x_continuous(
    breaks = c(-15,0,15),
    labels = c('-15 C', '0', '15')
  )+
  scale_y_continuous(
    position = 'right',
    limits = c(0, 30)
  )
```

    ## Warning: Removed 302 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

What about colors …

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = 'Min daily temp (Degree C)',
    y = 'Max daily temp',
    color = 'Location',
    caption = 'Max vs min daily temp in three locations; data from rnoaa'
  ) +
  scale_color_hue(h = c(100,300))
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = 'Min daily temp (Degree C)',
    y = 'Max daily temp',
    color = 'Location',
    caption = 'Max vs min daily temp in three locations; data from rnoaa'
  ) +
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

## Themes

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = 'Min daily temp (Degree C)',
    y = 'Max daily temp',
    color = 'Location',
    caption = 'Max vs min daily temp in three locations; data from rnoaa'
  ) +
  viridis::scale_color_viridis(discrete = TRUE) +
  theme_bw() +     # theme_minimal, theme_classic()
  theme(legend.position = 'bottom') 
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

## Data argument

``` r
weather_df |>
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

``` r
nyc_weather_df = 
  weather_df |>
  filter(name == 'CentralPark_NY')

hawaii_weather_df =
  weather_df |>
  filter(name =='Molokai_HI')

ggplot(nyc_weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = hawaii_weather_df)
```

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-7-2.png" width="90%" />

## ‘Patchwork’

``` r
weather_df |>
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

``` r
ggp_temp_scatter  =
  weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  theme(legend.position = 'none')


ggp_prcp_density  =
  weather_df |>
  filter(prcp >25) |>
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.5) +
  theme(legend.position = 'none')

ggp_tmax_date = 
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  theme(legend.position = "bottom")

(ggp_temp_scatter + ggp_prcp_density) / ggp_tmax_date
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

## data manipulation

``` r
weather_df |>
  mutate(
    name = forcats::fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA"))) |> 
  ggplot(aes(x = name, y = tmax)) + 
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

``` r
weather_df |>
  mutate(
    name = fct_reorder(name, tmax)
  ) |>
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin()
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 17 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-10-2.png" width="90%" />

## Complicate FAS plot

``` r
litters_df =
  read.csv('data/FAS_litters.csv') |>
  janitor::clean_names() |>
  separate(group, into = c('dose', 'day_of_tx'), sep = 3)

pups_df =
  read.csv('data/FAS_pups.csv') |>
  janitor::clean_names() 

fas_df =
  left_join(pups_df, litters_df, by = 'litter_number')

fas_df |>
  select(dose, day_of_tx, starts_with('pd')) |>
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome", 
    values_to = "pn_day"
    ) |>
  drop_na() |>
  mutate(outcome = fct_reorder(outcome, pn_day)) |>
    ggplot(aes(x = dose, y = pn_day)) +
    geom_violin() +
    facet_grid(day_of_tx ~ outcome)
```

<img src="viz_part2_real_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />
