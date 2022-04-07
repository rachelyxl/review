data\_tidy
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## pivot\_longer

load the PULSE data

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

wide format to long format..

``` r
pulse_data_tidy = 
  pulse_data %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visits",
    names_prefix = "bd_score_",
    values_to = "bdi")
```

Rewrite, combine, and extend (to add a mutate)

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visits",
    names_prefix = "bdi_score_",
    values_to = "bdi") %>% 
  relocate(id, visits) %>% 
  mutate(visits = recode(visits, "bl" = "00m"))

pulse_data
```

    ## # A tibble: 4,348 × 5
    ##       id visits   age sex     bdi
    ##    <dbl> <chr>  <dbl> <chr> <dbl>
    ##  1 10003 00m     48.0 male      7
    ##  2 10003 01m     48.0 male      1
    ##  3 10003 06m     48.0 male      2
    ##  4 10003 12m     48.0 male      0
    ##  5 10015 00m     72.5 male      6
    ##  6 10015 01m     72.5 male     NA
    ##  7 10015 06m     72.5 male     NA
    ##  8 10015 12m     72.5 male     NA
    ##  9 10022 00m     58.5 male     14
    ## 10 10022 01m     58.5 male      3
    ## # … with 4,338 more rows

## pivot wider

make up some data

``` r
analysis_result = 
  tibble(
    group = c("treatment", "treatmnet", "placebo","placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )

analysis_result %>% 
  pivot_wider(
    names_from = "time", 
    values_from = "mean"
  )
```

    ## # A tibble: 3 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4      NA
    ## 2 treatmnet  NA       8
    ## 3 placebo     3.5     4
