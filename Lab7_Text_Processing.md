Laboratorio 7: Text Processing
================
Christian Barrios
2024-10-19

## Cargar librerías necesarias

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
library(tm)
```

    ## Warning: package 'tm' was built under R version 4.3.3

    ## Loading required package: NLP

    ## Warning: package 'NLP' was built under R version 4.3.3

``` r
library(wordcloud)
```

    ## Warning: package 'wordcloud' was built under R version 4.3.3

    ## Loading required package: RColorBrewer

``` r
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 4.3.3

    ## 
    ## Attaching package: 'ggplot2'

    ## The following object is masked from 'package:NLP':
    ## 
    ##     annotate

## Cargar los datasets

``` r
metadata <- read.csv('Health_and_Personal_Care_metadata.csv', stringsAsFactors = FALSE)
reviews <- read.csv('Health_and_Personal_Care.csv', stringsAsFactors = FALSE)
```

## Pregunta 1: Cuántos productos contienen reviews con las palabras “love”, “recommend” y “enjoy”?

``` r
keywords <- c('love', 'recommend', 'enjoy')
filtered_reviews <- reviews %>%
  filter(grepl(paste(keywords, collapse = '|'), text, ignore.case = TRUE))

# Obtener la cantidad de productos 
unique_products <- length(unique(filtered_reviews$parent_id))

cat('Total de productos con reviews que contienen las palabras:', unique_products, '\n')
```

    ## Total de productos con reviews que contienen las palabras: 25424

## Pregunta 2: Top 5 de tiendas que venden esos productos

``` r
filtered_reviews_metadata <- filtered_reviews %>%
  inner_join(metadata, by = 'parent_id')

# Agrupar por tienda y contar el número de productos únicos
top_stores <- filtered_reviews_metadata %>%
  group_by(store) %>%
  summarise(product_count = n_distinct(parent_id)) %>%
  arrange(desc(product_count)) %>%
  head(5)

cat('Top 5 tiendas:\n')
```

    ## Top 5 tiendas:

``` r
print(top_stores)
```

    ## # A tibble: 5 × 2
    ##   store       product_count
    ##   <chr>               <int>
    ## 1 ""                    914
    ## 2 "HAARBB"              212
    ## 3 "Eyekepper"           173
    ## 4 "Generic"              84
    ## 5 "Glade"                58

## Pregunta 3: Generar un wordcloud sin stopwords de los reviews de la pregunta 1

``` r
reviews_corpus <- Corpus(VectorSource(filtered_reviews$text))
reviews_corpus <- tm_map(reviews_corpus, content_transformer(tolower))
```

    ## Warning in tm_map.SimpleCorpus(reviews_corpus, content_transformer(tolower)):
    ## transformation drops documents

``` r
reviews_corpus <- tm_map(reviews_corpus, removePunctuation)
```

    ## Warning in tm_map.SimpleCorpus(reviews_corpus, removePunctuation):
    ## transformation drops documents

``` r
reviews_corpus <- tm_map(reviews_corpus, removeNumbers)
```

    ## Warning in tm_map.SimpleCorpus(reviews_corpus, removeNumbers): transformation
    ## drops documents

``` r
reviews_corpus <- tm_map(reviews_corpus, removeWords, stopwords('en'))
```

    ## Warning in tm_map.SimpleCorpus(reviews_corpus, removeWords, stopwords("en")):
    ## transformation drops documents

``` r
reviews_corpus <- tm_map(reviews_corpus, stripWhitespace)
```

    ## Warning in tm_map.SimpleCorpus(reviews_corpus, stripWhitespace): transformation
    ## drops documents

``` r
wordcloud(reviews_corpus, max.words = 100, random.order = FALSE)
```

![](Lab7_Text_Processing_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Pregunta 4: Generar un wordcloud de los reviews de las 5 tiendas encontradas

``` r
top_stores_list <- top_stores$store

# Filtrar los reviews de las tiendas en el top 5
top_stores_reviews <- filtered_reviews_metadata %>%
  filter(store %in% top_stores_list)

top_stores_reviews <- top_stores_reviews %>%
  filter(!is.na(text) & text != '')

top_stores_corpus <- Corpus(VectorSource(top_stores_reviews$text))

top_stores_corpus <- tm_map(top_stores_corpus, content_transformer(tolower))
```

    ## Warning in tm_map.SimpleCorpus(top_stores_corpus,
    ## content_transformer(tolower)): transformation drops documents

``` r
top_stores_corpus <- tm_map(top_stores_corpus, removePunctuation)
```

    ## Warning in tm_map.SimpleCorpus(top_stores_corpus, removePunctuation):
    ## transformation drops documents

``` r
top_stores_corpus <- tm_map(top_stores_corpus, removeNumbers)
```

    ## Warning in tm_map.SimpleCorpus(top_stores_corpus, removeNumbers):
    ## transformation drops documents

``` r
top_stores_corpus <- tm_map(top_stores_corpus, removeWords, stopwords('en'))
```

    ## Warning in tm_map.SimpleCorpus(top_stores_corpus, removeWords,
    ## stopwords("en")): transformation drops documents

``` r
top_stores_corpus <- tm_map(top_stores_corpus, stripWhitespace)
```

    ## Warning in tm_map.SimpleCorpus(top_stores_corpus, stripWhitespace):
    ## transformation drops documents

``` r
# Generar el wordcloud
wordcloud(top_stores_corpus, max.words = 100, random.order = FALSE, scale = c(2, 0.25))
```

![](Lab7_Text_Processing_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Pregunta 5: Cuáles son las 25 palabras más frecuentes de los reviews

``` r
dtm <- TermDocumentMatrix(reviews_corpus)
word_freqs <- slam::row_sums(dtm)
word_freqs <- sort(word_freqs, decreasing = TRUE)
word_freqs_df <- data.frame(word = names(word_freqs), freq = word_freqs)

cat('Las 25 palabras más frecuentes:\n')
```

    ## Las 25 palabras más frecuentes:

``` r
print(head(word_freqs_df, 25))
```

    ##                word  freq
    ## love           love 53659
    ## product     product 28816
    ## recommend recommend 25083
    ## great         great 22508
    ## use             use 22097
    ## one             one 20063
    ## like           like 19440
    ## just           just 16578
    ## can             can 15130
    ## will           will 14014
    ## really       really 13708
    ## get             get 13318
    ## well           well 13295
    ## good           good 13180
    ## time           time 10787
    ## also           also 10661
    ## used           used 10459
    ## much           much 10054
    ## using         using 10041
    ## easy           easy  9633
    ## highly       highly  9452
    ## works         works  9008
    ## little       little  8779
    ## work           work  8316
    ## dont           dont  7826

``` r
top_25_words <- head(word_freqs_df, 25)
ggplot(top_25_words, aes(x = reorder(word, freq), y = freq)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  coord_flip() +
  xlab('Palabra') +
  ylab('Frecuencia') +
  ggtitle('Top 25 palabras más frecuentes en los reviews')
```

![](Lab7_Text_Processing_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
