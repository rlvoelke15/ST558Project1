ST 558 Project 1
================
Rebecca Voelker
10/05/2021

## **Render Code (for Reference ONLY)**

``` r
rmarkdown::render("input_test.Rmd", "github_document","README.md")
```

## **Packages Required for ST 558 Project 1**

1.  httr - *Retrieves Data from API*
2.  jsonlite - *Parses Results from API Query*
3.  dplyr -

``` r
library(httr)
library(jsonlite)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

## This function call, calls in a list of a user-specified country and it’s slug-code

``` r
CountryFun <- function(country){
  base <- "https://api.covid19api.com/live/country/"
  endpoint <- country
  paste <- paste(base, endpoint, sep = "")
  outputAPI <- fromJSON(paste)
  return(as_tibble(outputAPI))
}

Country <- CountryFun("australia")
```

## This function will call the details of COVID cases in in particular country

``` r
funCountry <- function(country){
  API_init <- GET("https://api.covid19api.com/summary")
  API_content <- content(API_init, as = "text")
  API_JSON <- fromJSON(API_content)
  output <- API_JSON$Countries %>% select(ID:TotalRecovered) %>% filter(Country == country)
}

SATest <- funCountry("South Africa") 
```

## This function will call the details of Confirmed Cases that are &gt;= X (User-Defined) \# of Cases in Australia

``` r
funAUS_Confirm <- function(x){
  API_init <- GET("https://api.covid19api.com/live/country/australia")
  API_content <- content(API_init, as = "text")
  API_JSON <- fromJSON(API_content)
  output <- API_JSON %>% filter(Confirmed >= x)
  return(output)
}

AUSConfirmTest <- funAUS_Confirm(50000)
```

## This function will call the details of COVID cases in a particular state in the United States

``` r
funState <- function(province){
  API_init <- GET("https://api.covid19api.com/live/country/USA")
  API_content <- content(API_init, as = "text")
  API_JSON <- fromJSON(API_content)
  output <- API_JSON %>% filter(Province == province)
  return(output)
}

WISTest <- funState("Wisconsin")
```

## This function will call the details of the first instance of COVID cases &gt;= X (User-Defined) \# of Confirmed Cases in any state in the United States

``` r
funUS_Confirm <- function(x){
  API_init <- GET("https://api.covid19api.com/live/country/USA")
  API_content <- content(API_init, as = "text")
  API_JSON <- fromJSON(API_content)
  output <- API_JSON %>% filter(Confirmed >= x) %>% arrange(Province, Confirmed)
  output_subset <- output[match(unique(output$Province), output$Province),]
  return(output_subset)
}

ConfirmTest <- funUS_Confirm(100000)
```

## This function will call the details of the first instance of Deaths &gt;= X (User-Defined) \# in any state in the United States

``` r
funUS_Death <-function(x){
  API_init <- GET("https://api.covid19api.com/live/country/USA")
  API_content <- content(API_init, as = "text")
  API_JSON <- fromJSON(API_content)
  output <- API_JSON %>% filter(Deaths >= x) %>% arrange(Province, Deaths)
  output_subset <- output[match(unique(output$Province), output$Province),]
  return(output_subset)
}

funUS_DeathTest <- funUS_Death(5000)
```
