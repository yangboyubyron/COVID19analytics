
<!-- README.md is generated from README.Rmd. Please edit that file -->

# COVID19analytics

<!-- . -->

This package curate (downloads, clean, consolidate, smooth) [data from
Johns Hokpins](https://github.com/CSSEGISandData/COVID-19/) for
analysing international outbreak of COVID-19.

It includes several visualizations of the COVID-19 international
outbreak.

Yanchang Zhao, COVID-19 Data Analysis with Tidyverse and Ggplot2 -
China. RDataMining.com, 2020.

URL:
<http://www.rdatamining.com/docs/Coronavirus-data-analysis-china.pdf>.

  - COVID19DataProcessor generates curated series
  - [visualizations](https://www.r-bloggers.com/coronavirus-data-analysis-with-r-tidyverse-and-ggplot2/)
    by [Yanchang Zhao](https://www.r-bloggers.com/author/yanchang-zhao/)
    are included in ReportGenerator R6 object
  - More visualizations included int ReportGeneratorEnhanced R6 object
  - Visualizations ReportGeneratorDataComparison compares all countries
    counting epidemy day 0 when confirmed cases \> n (i.e. n = 100).

# Consideration

Data is still noisy because there are missing data from some regions in
some days. We are working on in it.

# Package

<!-- badges: start -->

| Release                                                                                                              | Usage                                                                                                    | Development                                                                                                                                                                                            |
| :------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                                                                                      | [![minimal R version](https://img.shields.io/badge/R%3E%3D-3.4.0-blue.svg)](https://cran.r-project.org/) | [![Travis](https://travis-ci.org/rOpenStats/COVID19analytics.svg?branch=master)](https://travis-ci.org/rOpenStats/COVID19analytics)                                                                    |
| [![CRAN](http://www.r-pkg.org/badges/version/COVID19analytics)](https://cran.r-project.org/package=COVID19analytics) |                                                                                                          | [![codecov](https://codecov.io/gh/rOpenStats/COVID19analytics/branch/master/graph/badge.svg)](https://codecov.io/gh/rOpenStats/COVID19analytics)                                                       |
|                                                                                                                      |                                                                                                          | [![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active) |

<!-- badges: end -->

# How to get started (Development version)

Install the R package using the following commands on the R console:

``` r
# install.packages("devtools")
devtools::install_github("rOpenStats/COVID19analytics", build_opts = NULL)
```

# How to use it

``` r
library(COVID19analytics) 
#> Warning: replacing previous import 'ggplot2::Layout' by 'lgr::Layout' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::intersect' by 'lubridate::intersect'
#> when loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::union' by 'lubridate::union' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::setdiff' by 'lubridate::setdiff' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::equals' by 'testthat::equals' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::not' by 'testthat::not' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::is_less_than' by
#> 'testthat::is_less_than' when loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::matches' by 'testthat::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'testthat::matches' by 'tidyr::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::extract' by 'tidyr::extract' when
#> loading 'COVID19analytics'
library(dplyr) 
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

``` r
data.processor <- COVID19DataProcessor$new(force.download = FALSE)
dummy <- data.processor$setupData()
#> INFO  [10:14:05.306]  {stage: data loaded}
dummy <- data.processor$curate()
#> Warning in countrycode(x, origin = "country.name", destination = "continent"): Some values were not matched unambiguously: MS Zaandam
#> INFO  [10:14:07.535]  {stage: consolidated}
#> INFO  [10:14:08.289]  {stage: Starting first imputation}
#> INFO  [10:14:08.290] Imputation indicator {indicator: confirmed}
#> INFO  [10:14:08.327] Imputation indicator {indicator: recovered}
#> INFO  [10:14:08.417] Imputation indicator {indicator: deaths}
#> INFO  [10:14:09.664]  {stage: Calculating top countries}
current.date <- max(data.processor$data$date)

rg <- ReportGeneratorEnhanced$new(data.processor)
rc <- ReportGeneratorDataComparison$new(data.processor = data.processor)

top.countries <- data.processor$top.countries
international.countries <- unique(c(data.processor$top.countries,
                                    "Japan", "Singapore", "Korea, South"))
latam.countries <- sort(c("Mexico",
                     data.processor$countries$getCountries(division = "sub.continent", name = "Caribbean"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "Central America"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "South America")))
```

``` r
# Top 10 daily cases confirmed increment
(data.processor$data %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc, imputation.confirmed) %>%
  arrange(desc(confirmed.inc)) %>%
  filter(confirmed >=10))[1:10,]
#>           country       date rate.inc.daily confirmed.inc confirmed deaths
#> 1              US 2020-05-09           0.02         25621   1309550  78795
#> 2          Russia 2020-05-09           0.06         10817    198676   1827
#> 3          Brazil 2020-05-09           0.06          9167    156061  10656
#> 4  United Kingdom 2020-05-09           0.02          3896    216525  31662
#> 5            Peru 2020-05-09           0.05          3168     65015   1814
#> 6           India 2020-05-09           0.05          3113     62808   2101
#> 7        Pakistan 2020-05-09           0.09          2301     28736    636
#> 8          Mexico 2020-05-09           0.06          1938     33460   3353
#> 9    Saudi Arabia 2020-05-09           0.05          1704     37136    239
#> 10         Turkey 2020-05-09           0.01          1546    137115   3739
#>    deaths.inc imputation.confirmed
#> 1        1615                     
#> 2         104                     
#> 3         639                     
#> 4         346                     
#> 5         100                     
#> 6         116                     
#> 7          37                     
#> 8         193                     
#> 9          10                     
#> 10         50
```

``` r
# Top 10 daily deaths increment
(data.processor$data %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc, imputation.confirmed) %>%
  arrange(desc(deaths.inc)))[1:10,]
#>           country       date rate.inc.daily confirmed.inc confirmed deaths
#> 1              US 2020-05-09           0.02         25621   1309550  78795
#> 2          Brazil 2020-05-09           0.06          9167    156061  10656
#> 3  United Kingdom 2020-05-09           0.02          3896    216525  31662
#> 4           Italy 2020-05-09           0.00          1083    218268  30395
#> 5          Mexico 2020-05-09           0.06          1938     33460   3353
#> 6           Spain 2020-05-09           0.00           721    223578  26478
#> 7          Canada 2020-05-09           0.02          1244     68918   4823
#> 8           India 2020-05-09           0.05          3113     62808   2101
#> 9          Russia 2020-05-09           0.06         10817    198676   1827
#> 10           Peru 2020-05-09           0.05          3168     65015   1814
#>    deaths.inc imputation.confirmed
#> 1        1615                     
#> 2         639                     
#> 3         346                     
#> 4         194                     
#> 5         193                     
#> 6         179                     
#> 7         126                     
#> 8         116                     
#> 9         104                     
#> 10        100
```

``` r
rg$ggplotTopCountriesStackedBarDailyInc(included.countries = latam.countries,
                                                  map.region = "Latam")
```

<img src="man/figures/README-unnamed-chunk-5-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, min.cases = 20)
```

<img src="man/figures/README-unnamed-chunk-5-2.png" width="100%" />

``` r

rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "confirmed.inc", log.scale = TRUE)
```

<img src="man/figures/README-unnamed-chunk-5-3.png" width="100%" />

``` r
rg$ggplotTopCountriesStackedBarDailyInc(top.countries)
#> Warning: Removed 2 rows containing missing values (position_stack).
```

<img src="man/figures/README-unnamed-chunk-6-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                               min.cases = 100)
```

<img src="man/figures/README-unnamed-chunk-6-2.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "confirmed.inc", log.scale = TRUE)
```

<img src="man/figures/README-unnamed-chunk-7-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "rate.inc.daily", log.scale = TRUE)
#> Warning: Transformation introduced infinite values in continuous y-axis
```

<img src="man/figures/README-unnamed-chunk-7-2.png" width="100%" />

``` r
rg$ggplotTopCountriesPie()
#> Scale for 'fill' is already present. Adding another scale for 'fill', which
#> will replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-8-1.png" width="100%" />

``` r
rg$ggplotTopCountriesBarPlots()
```

<img src="man/figures/README-unnamed-chunk-8-2.png" width="100%" />

``` r
rg$ggplotCountriesBarGraphs(selected.country = "Argentina")
```

<img src="man/figures/README-unnamed-chunk-9-1.png" width="100%" />
