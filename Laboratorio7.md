Laboratorio#7
================
Christian Barrios
2024-11-15

``` r
library(readr)  
```

    ## Warning: package 'readr' was built under R version 4.3.3

``` r
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

``` r
library(stringr)  
library(lubridate)  
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
library(stopwords)  
```

    ## Warning: package 'stopwords' was built under R version 4.3.3

``` r
library(wordcloud) 
```

    ## Warning: package 'wordcloud' was built under R version 4.3.3

    ## Loading required package: RColorBrewer

``` r
knitr::opts_chunk$set(warning = FALSE, message = FALSE)
```

``` r
metadata <- read_csv("C:/Users/barri/Documents/UFM/2024/Segundo Semestre/Data Wrangling/Laboratorio7-TextProcessing/Health_and_Personal_Care_metadata.csv")
reviews <- read_csv("C:/Users/barri/Documents/UFM/2024/Segundo Semestre/Data Wrangling/Laboratorio7-TextProcessing/Health_and_Personal_Care.csv")
```

## 1) Identificar productos con palabras clave en reseñas

``` r
keywords <- c("love", "recommend", "enjoy")
pattern <- paste(keywords, collapse = '|')

# Filtrar productos con reseñas que contengan las palabras clave
products_with_keywords <- reviews %>%
  filter(str_detect(text, pattern)) %>%
  select(product_id) %>%
  distinct() %>%
  summarise(product_count = n())

products_with_keywords
```

    ## # A tibble: 1 × 1
    ##   product_count
    ##           <int>
    ## 1         23180

## 2) Tiendas principales que venden los productos identificados

``` r
# Relacionar productos con las tiendas
store_summary <- reviews %>%
  filter(str_detect(text, pattern)) %>%
  distinct(product_id, parent_id) %>%
  inner_join(metadata, by = "parent_id") %>%
  filter(!is.na(store)) %>%
  group_by(store) %>%
  summarise(total_products = n()) %>%
  arrange(desc(total_products)) %>%
  slice_head(n = 5)

store_summary
```

    ## # A tibble: 5 × 2
    ##   store       total_products
    ##   <chr>                <int>
    ## 1 HAARBB                 173
    ## 2 Eyekepper              135
    ## 3 US Organic              78
    ## 4 Andaz Press             76
    ## 5 Generic                 74

## 3) Wordcloud de palabras positivas en reseñas

``` r
# Crear lista de palabras filtradas
positive_reviews <- reviews %>%
  filter(str_detect(text, pattern)) %>%
  pull(text)

stop_words <- c(stopwords("en"), stopwords("es"))
words <- str_split(positive_reviews[1:100], boundary("word")) %>% unlist()

filtered_words <- tibble(word = words) %>%
  filter(!word %in% stop_words) %>%
  count(word, sort = TRUE)

wordcloud(filtered_words$word, filtered_words$n)
```

![](Laboratorio7_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## 4) Wordcloud para reseñas de tiendas principales

``` r
# Seleccionar IDs de las tiendas principales
selected_stores <- store_summary$store
parent_ids <- metadata %>%
  filter(store %in% selected_stores) %>%
  pull(parent_id)

# Filtrar reseñas de estas tiendas
store_reviews <- reviews %>%
  filter(parent_id %in% parent_ids) %>%
  pull(text)

store_words <- str_split(store_reviews[1:100], boundary("word")) %>% unlist()

filtered_store_words <- tibble(word = store_words) %>%
  filter(!word %in% stop_words) %>%
  count(word, sort = TRUE)

wordcloud(filtered_store_words$word, filtered_store_words$n)
```

![](Laboratorio7_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## 5) Las 25 palabras más frecuentes

``` r
# Extraer todas las palabras de las reseñas
all_words <- reviews %>%
  pull(text) %>%
  str_split(boundary("word")) %>%
  unlist()

# Contar frecuencias
frequencies <- tibble(word = all_words) %>%
  filter(!word %in% stop_words) %>%
  count(word, sort = TRUE)

top_25 <- frequencies %>%
  slice_head(n = 25)

top_25
```

    ## # A tibble: 25 × 2
    ##    word         n
    ##    <chr>    <int>
    ##  1 I       590693
    ##  2 br      136733
    ##  3 product 100611
    ##  4 The      94876
    ##  5 It       81424
    ##  6 use      80636
    ##  7 like     76352
    ##  8 great    71485
    ##  9 This     69662
    ## 10 one      64936
    ## # ℹ 15 more rows
