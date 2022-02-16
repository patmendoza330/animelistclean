2/13/2022

# Cleaning MyAnimeList data for Google Data Analytics Capstone

In the [previous](https://github.com/patmendoza330/animelistextract) script I had downloaded all of the data that I wanted to
use for my capstone project. Now I need to explore and clean all of it.

## First things first - setting some variables

Set your working directory to wherever you’d like in the
`WORKINDIRECTORY` section.

``` r
wd1 = "WORKINGDIRECTORY"
setwd(wd1)
```

## Install and load any libraries

The `tidyverse` will be used to manipulate/transform data and the
`janitor` library will be used to check duplicates and consistency of
data.

``` r
install.packages(c("tidyverse", "janitor"))
```

Next, we want to load the two libraries:

``` r
library(tidyverse)
library(janitor)
```

## Load in the data

[Previously](https://github.com/patmendoza330/animelistextract), we had downloaded and made transformations to data
from the MyAnimeList API. Now we’re going to load that data into R for
further analysis and cleaning.

``` r
anime_demo_table <- read.delim("temp_anime_demo_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
anime_genres_table <- read.delim("temp_anime_genres_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
anime_ranking_table <- read.delim("temp_anime_ranking_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
anime_studios_table <- read.delim("temp_anime_studios_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
anime_syn_table <- read.delim("temp_anime_syn_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
anime_table <- read.delim("temp_anime_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
demo_l <- read.delim("temp_demo_l.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
genres_l <- read.delim("temp_genres_l.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
rank_table <- read.delim("temp_rank_table.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
studios_l <- read.delim("temp_studios_l.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
tm_ky <- read.delim("temp_tm_ky.csv", header = TRUE, sep = ",", stringsAsFactors = FALSE)
```

## Clean the data

I break cleaning and data integrity into the following steps:

1.  Adjust any field types (e.g. character columns that need to be
    numeric)
2.  Cleaning up column names
3.  Adjusting strings to eliminate trailing, leading, and unnecessary
    spaces as well as replacing any double quotes with single quotes
4.  Check for nulls
5.  Check for duplicates
6.  Check for inconsistencies in naming conventions (within reason).

### Adjust field types

``` r
glimpse(anime_demo_table)
```

    ## Rows: 240
    ## Columns: 3
    ## $ tm_ky   <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,~
    ## $ id      <int> 10030, 10049, 10162, 10165, 10271, 10379, 1065, 10800, 11061, ~
    ## $ demo_id <int> 27, 27, 43, 27, 42, 25, 27, 43, 27, 42, 43, 25, 27, 27, 42, 27~

``` r
glimpse(anime_genres_table)
```

    ## Rows: 1,517
    ## Columns: 3
    ## $ tm_ky     <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, ~
    ## $ id        <int> 1, 1, 1, 1, 1, 1, 10030, 10030, 10030, 10049, 10049, 10049, ~
    ## $ genres_id <int> 1, 2, 24, 29, 4, 8, 22, 4, 8, 1, 37, 6, 1, 10, 37, 36, 23, 3~

``` r
glimpse(anime_ranking_table)
```

    ## Rows: 393
    ## Columns: 12
    ## $ tm_ky                        <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,~
    ## $ id                           <int> 5114, 48583, 38524, 9253, 28977, 42938, 9~
    ## $ mean                         <dbl> 9.15, 9.15, 9.09, 9.09, 9.09, 9.06, 9.06,~
    ## $ rank                         <int> 1, 2, 3, 4, 5, 6, 7, 9, 10, 12, 14, 16, 1~
    ## $ popularity                   <int> 3, 229, 32, 13, 336, 623, 381, 9, 684, 73~
    ## $ num_scoring_users            <int> 1690301, 144781, 1182762, 1152310, 178769~
    ## $ statistics.watching          <int> 212433, 381690, 66687, 139749, 56757, 318~
    ## $ statistics.completed         <int> 2017696, 138, 1519325, 1359955, 210439, 1~
    ## $ statistics.on_hold           <int> 94782, 3199, 7346, 75881, 20221, 4150, 12~
    ## $ statistics.dropped           <int> 42850, 1303, 4657, 44659, 14120, 2613, 84~
    ## $ statistics.plan_to_watch     <int> 413589, 266683, 119020, 541799, 207021, 1~
    ## $ statistics.num_scoring_users <int> 2781350, 653013, 1717035, 2162043, 508558~

``` r
glimpse(anime_studios_table)
```

    ## Rows: 412
    ## Columns: 3
    ## $ tm_ky     <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, ~
    ## $ id        <int> 1, 10030, 10049, 10087, 10162, 10165, 10271, 10379, 1065, 10~
    ## $ studio_id <int> 14, 7, 37, 43, 10, 2, 11, 112, 280, 36, 86, 112, 11, 103, 11~

``` r
glimpse(anime_syn_table)
```

    ## Rows: 555
    ## Columns: 3
    ## $ tm_ky    <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2~
    ## $ id       <int> 5114, 5114, 5114, 5114, 48583, 48583, 28977, 42938, 42938, 42~
    ## $ synonyms <chr> "Hagane no Renkinjutsushi: Fullmetal Alchemist", "Fullmetal A~

``` r
glimpse(anime_table)
```

    ## Rows: 393
    ## Columns: 22
    ## $ tm_ky                 <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, ~
    ## $ id                    <int> 5114, 48583, 38524, 9253, 28977, 42938, 9969, 11~
    ## $ title                 <chr> "Fullmetal Alchemist: Brotherhood", "Shingeki no~
    ## $ main_picture.medium   <chr> "https://api-cdn.myanimelist.net/images/anime/12~
    ## $ main_picture.large    <chr> "https://api-cdn.myanimelist.net/images/anime/12~
    ## $ alternative_titles.en <chr> "Fullmetal Alchemist: Brotherhood", "Attack on T~
    ## $ alternative_titles.ja <chr> "<U+92FC><U+306E><U+932C><U+91D1><U+8853><U+5E2B~
    ## $ start_date            <chr> "2009-04-05", "2022-01-10", "2019-04-29", "2011-~
    ## $ end_date              <chr> "2010-07-04", NA, "2019-07-01", "2011-09-14", "2~
    ## $ synopsis              <chr> "After a horrific alchemy experiment goes wrong ~
    ## $ media_type            <chr> "tv", "tv", "tv", "tv", "tv", "tv", "tv", "tv", ~
    ## $ status                <chr> "finished_airing", "currently_airing", "finished~
    ## $ num_episodes          <int> 64, 0, 10, 24, 51, 13, 51, 148, 13, 12, 22, 24, ~
    ## $ start_season.year     <int> 2009, 2022, 2019, 2011, 2015, 2021, 2011, 2011, ~
    ## $ start_season.season   <chr> "spring", "winter", "spring", "spring", "spring"~
    ## $ rating                <chr> "r", "r", "r", "pg_13", "pg_13", "pg_13", "pg_13~
    ## $ nsfw                  <chr> "white", "white", "white", "white", "white", "wh~
    ## $ demo_de               <chr> "Shounen", "Shounen", "Shounen", NA, "Shounen", ~
    ## $ genres_de             <chr> "Action,Fantasy,Adventure,Military,Comedy,Drama"~
    ## $ studios_de            <chr> "Bones", "MAPPA", "Wit Studio", "White Fox", "Ba~
    ## $ synonyms              <chr> "Hagane no Renkinjutsushi: Fullmetal Alchemist,F~
    ## $ alternative_title     <chr> "Fullmetal Alchemist: Brotherhood", "Attack on T~

``` r
glimpse(demo_l)
```

    ## Rows: 5
    ## Columns: 3
    ## $ tm_ky   <int> 2, 2, 2, 2, 2
    ## $ demo_id <int> 15, 25, 27, 42, 43
    ## $ demo_de <chr> "Kids", "Shoujo", "Shounen", "Seinen", "Josei"

``` r
glimpse(genres_l)
```

    ## Rows: 35
    ## Columns: 3
    ## $ tm_ky     <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, ~
    ## $ genres_id <int> 1, 10, 11, 13, 14, 17, 18, 19, 2, 20, 21, 22, 23, 24, 26, 28~
    ## $ genres_de <chr> "Action", "Fantasy", "Game", "Historical", "Horror", "Martia~

``` r
glimpse(rank_table)
```

    ## Rows: 78,336
    ## Columns: 5
    ## $ tm_ky         <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,~
    ## $ id            <int> 5114, 48583, 38524, 9253, 28977, 42938, 9969, 39486, 110~
    ## $ title         <chr> "Fullmetal Alchemist: Brotherhood", "Shingeki no Kyojin:~
    ## $ rank          <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 1~
    ## $ rank_category <chr> "all", "all", "all", "all", "all", "all", "all", "all", ~

``` r
glimpse(studios_l)
```

    ## Rows: 82
    ## Columns: 3
    ## $ tm_ky     <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, ~
    ## $ studio_id <int> 1, 10, 101, 103, 1075, 11, 110, 1103, 1109, 1119, 112, 1129,~
    ## $ studio_de <chr> "Studio Pierrot", "Production I.G", "Studio Hibari", "Tatsun~

``` r
glimpse(tm_ky)
```

    ## Rows: 1
    ## Columns: 2
    ## $ tm_ky    <int> 2
    ## $ tm_ky_de <chr> "2/11/22"

Even though the data downloaded had been primarily character based, when
the files are read into R, the correct field types are used.

It looks like no changes will be needed at this point.

### Cleaning up column names

I want to change all of the `id` fields to `mal_id`.

``` r
names(anime_demo_table)[names(anime_demo_table) == "id"] = "mal_id"
names(anime_genres_table)[names(anime_genres_table) == "id"] = "mal_id"
names(anime_ranking_table)[names(anime_ranking_table) == "id"] = "mal_id"
names(anime_studios_table)[names(anime_studios_table) == "id"] = "mal_id"
names(anime_syn_table)[names(anime_syn_table) == "id"] = "mal_id"
names(anime_table)[names(anime_table) == "id"] = "mal_id"
names(rank_table)[names(rank_table) == "id"] = "mal_id"
```

These are the column names that I’m expecting in each table:

1.  anime\_demo\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| demo\_id | int  |             |

2.  anime\_genres\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| genres\_id | int  | PK          |

3.  anime\_ranking\_table

| Field                          | Type | Primary Key |
|:-------------------------------|:-----|:------------|
| tm\_ky                         | int  | PK          |
| mal\_id                        | int  | PK          |
| mean                           | dbl  |             |
| rank                           | int  |             |
| popularity                     | int  |             |
| num\_scoring\_users            | int  |             |
| statistics.watching            | int  |             |
| statistics.completed           | int  |             |
| statistics.on\_hold            | int  |             |
| statistics.dropped             | int  |             |
| statistics.plan\_to\_watch     | int  |             |
| statistics.num\_scoring\_users | int  |             |

4.  anime\_studios\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| studio\_id | int  | PK          |

5.  anime\_syn\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| synonyms | chr  |             |

6.  anime\_table

| Field                  | Type | Primary Key |
|:-----------------------|:-----|:------------|
| tm\_ky                 | int  | PK          |
| mal\_id                | int  | PK          |
| title                  | chr  |             |
| main\_picture.medium   | chr  |             |
| main\_picture.large    | chr  |             |
| alternative\_titles.en | chr  |             |
| alternative\_titles.ja | chr  |             |
| start\_date            | chr  |             |
| end\_date              | chr  |             |
| synopsis               | chr  |             |
| media\_type            | chr  |             |
| status                 | chr  |             |
| num\_episodes          | int  |             |
| start\_season.year     | int  |             |
| start\_season.season   | chr  |             |
| rating                 | chr  |             |
| nsfw                   | chr  |             |
| demo\_de               | chr  |             |
| genres\_de             | chr  |             |
| studios\_de            | chr  |             |
| synonyms               | chr  |             |
| alternative\_title     | chr  |             |

7.  rank\_table

| Field          | Type | Primary Key |
|:---------------|:-----|:------------|
| tm\_ky         | int  | PK          |
| mal\_id        | int  | PK          |
| title          | chr  |             |
| rank           | int  |             |
| rank\_category | chr  | PK          |

8.  demo\_l

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| demo\_id | int  | PK          |
| demo\_de | chr  |             |

9.  genres\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| genres\_id | int  | PK          |
| genres\_de | chr  |             |

10. studios\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| studio\_id | int  | PK          |
| studio\_de | chr  |             |

11. tm\_ky

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| tm\_ky\_de | chr  |             |

``` r
anime_demo_table_Col <- c('tm_ky','mal_id','demo_id')
anime_genres_table_Col <- c('tm_ky','mal_id','genres_id')
anime_ranking_table_Col <- c('tm_ky','mal_id','mean','rank','popularity','num_scoring_users','statistics.watching','statistics.completed','statistics.on_hold','statistics.dropped','statistics.plan_to_watch','statistics.num_scoring_users')
anime_studios_table_Col <- c('tm_ky','mal_id','studio_id')
anime_syn_table_Col <- c('tm_ky','mal_id','synonyms')
anime_table_Col <- c('tm_ky','mal_id','title','main_picture.medium','main_picture.large','alternative_titles.en','alternative_titles.ja','start_date','end_date','synopsis','media_type','status','num_episodes','start_season.year','start_season.season','rating','nsfw','demo_de','genres_de','studios_de','synonyms','alternative_title')
rank_table_Col <- c('tm_ky','mal_id','title','rank','rank_category') 
demo_l_Col <- c('tm_ky','demo_id','demo_de')
genres_l_col <- c('tm_ky','genres_id','genres_de')
studios_l_Col <- c('tm_ky','studio_id','studio_de')
tm_ky_Col <- c('tm_ky','tm_ky_de')

identical(colnames(anime_demo_table), anime_demo_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(anime_genres_table), anime_genres_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(anime_ranking_table), anime_ranking_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(anime_studios_table), anime_studios_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(anime_syn_table), anime_syn_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(anime_table), anime_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(rank_table), rank_table_Col)
```

    ## [1] TRUE

``` r
identical(colnames(demo_l), demo_l_Col)
```

    ## [1] TRUE

``` r
identical(colnames(genres_l), genres_l_col)
```

    ## [1] TRUE

``` r
identical(colnames(studios_l), studios_l_Col)
```

    ## [1] TRUE

``` r
identical(colnames(tm_ky), tm_ky_Col)
```

    ## [1] TRUE

All of the column names match expectations, so we are good!

### Cleaning up strings

There is one situation that will cause issues when uploading onto
tableau. If the field includes characters for new line (`\n`) or
carriage returns (`\r`) the table loading process will fail.

I need to search through all tables that contain character fields and
confirm that they don’t have those characters.

``` r
tables1 <- list(anime_demo_table=anime_demo_table, anime_genres_table=anime_genres_table, anime_ranking_table=anime_ranking_table, anime_studios_table=anime_studios_table, anime_syn_table=anime_syn_table, anime_table=anime_table, rank_table=rank_table, demo_l=demo_l, genres_l=genres_l, studios_l=studios_l)

find_character <- function(df, dfName){
  for (i in 1:ncol(df)){
    if (is.character(df[,i])){
      if (TRUE %in% grep("\n",df[, i]) | TRUE %in% grep("\r",df[, i])){
        if (TRUE %in% grep("\n",df[, i])){
          print(paste(dfName, "-", names(df)[i], "column has the new line character"))
        }
        if (TRUE %in% grep("\r",df[, i])){
          print(paste(dfName, "-", names(df)[i], "column has the carriage return character"))
        }
      }
    }
  }
}

for (i in 1:length(tables1)){
  find_character(tables1[[i]], names(tables1)[i])
}
```

    ## [1] "anime_table - synopsis column has the new line character"

``` r
tables1 <- NULL
```

It appears that only the synopsis column contains the new line
character, so lets get rid of those.

``` r
anime_table$synopsis <- sapply(anime_table$synopsis, 
                             function(x) {gsub("[\n]","", x) })
```

Next, there are four character strings that I need to adjust for:

1.  Eliminate leading spaces
2.  Eliminate trailing spaces
3.  Eliminate unnecessary spaces
4.  Eliminate any double quotes and replace with single quotes

The following tables have character columns:

-   anime\_syn\_table
-   anime\_table
-   rank\_table
-   demo\_l
-   genres\_l
-   studios\_l

``` r
strFunctions <- function(df){
  df <- df %>%
    mutate(across(where(is.character), str_trim)) %>%
    mutate(across(where(is.character), str_squish)) %>%
    mutate(across(where(is.character), gsub, pattern='"', replacement="'"))
  return(df)
}

anime_syn_table <- strFunctions(anime_syn_table)
anime_table <- strFunctions(anime_table)
rank_table <- strFunctions(rank_table)
demo_l <- strFunctions(demo_l)
genres_l <- strFunctions(genres_l)
studios_l <- strFunctions(studios_l)
```

### Check for nulls

I will check for nulls in each table and assess whether or not I need to
address.

``` r
lapply(anime_demo_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $demo_id
    ## [1] 0

``` r
lapply(anime_genres_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $genres_id
    ## [1] 0

``` r
lapply(anime_ranking_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $mean
    ## [1] 0
    ## 
    ## $rank
    ## [1] 0
    ## 
    ## $popularity
    ## [1] 0
    ## 
    ## $num_scoring_users
    ## [1] 0
    ## 
    ## $statistics.watching
    ## [1] 0
    ## 
    ## $statistics.completed
    ## [1] 0
    ## 
    ## $statistics.on_hold
    ## [1] 0
    ## 
    ## $statistics.dropped
    ## [1] 0
    ## 
    ## $statistics.plan_to_watch
    ## [1] 0
    ## 
    ## $statistics.num_scoring_users
    ## [1] 0

``` r
lapply(anime_studios_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $studio_id
    ## [1] 0

``` r
lapply(anime_syn_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $synonyms
    ## [1] 0

``` r
lapply(anime_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $title
    ## [1] 0
    ## 
    ## $main_picture.medium
    ## [1] 0
    ## 
    ## $main_picture.large
    ## [1] 0
    ## 
    ## $alternative_titles.en
    ## [1] 0
    ## 
    ## $alternative_titles.ja
    ## [1] 0
    ## 
    ## $start_date
    ## [1] 0
    ## 
    ## $end_date
    ## [1] 8
    ## 
    ## $synopsis
    ## [1] 0
    ## 
    ## $media_type
    ## [1] 0
    ## 
    ## $status
    ## [1] 0
    ## 
    ## $num_episodes
    ## [1] 0
    ## 
    ## $start_season.year
    ## [1] 0
    ## 
    ## $start_season.season
    ## [1] 0
    ## 
    ## $rating
    ## [1] 0
    ## 
    ## $nsfw
    ## [1] 0
    ## 
    ## $demo_de
    ## [1] 153
    ## 
    ## $genres_de
    ## [1] 0
    ## 
    ## $studios_de
    ## [1] 0
    ## 
    ## $synonyms
    ## [1] 92
    ## 
    ## $alternative_title
    ## [1] 0

Ok, so the following fields contain nulls in the anime\_table:

-   end\_date
-   demo\_de
-   synonyms

I plan on incorporating the demographic into my analysis, because of
this, I will replace the null values in this field with `missing`.

``` r
anime_table$demo_de <- anime_table$demo_de %>%
  replace_na('missing')

# if I need to replace multiple columns, use the below code
# anime_table <- anime_table %>%
#   replace_na(list(x='missing', y = 'none'))

# When I run this for the first full time I will also encounter missing values in start_season_year which will require a substitution of the first four characters from the start_date

# anime_table <- anime_table %>%
#   mutate(start_season.year = case_when(
#     is.na(start_season.year) ~ as.integer(substr(start_date, 1, 4)),
#     !is.na(start_season.year) ~ start_season.year))
# lapply(anime_table,function(x) { length(which(is.na(x)))})
```

``` r
lapply(rank_table,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $mal_id
    ## [1] 0
    ## 
    ## $title
    ## [1] 0
    ## 
    ## $rank
    ## [1] 0
    ## 
    ## $rank_category
    ## [1] 0

``` r
lapply(demo_l,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $demo_id
    ## [1] 0
    ## 
    ## $demo_de
    ## [1] 0

``` r
lapply(genres_l,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $genres_id
    ## [1] 0
    ## 
    ## $genres_de
    ## [1] 0

``` r
lapply(studios_l,function(x) { length(which(is.na(x)))})
```

    ## $tm_ky
    ## [1] 0
    ## 
    ## $studio_id
    ## [1] 0
    ## 
    ## $studio_de
    ## [1] 0

All other tables look ok, so we’re good to move onto the next step.

### Check for duplicates

I’ll check for duplicates for all fields first, then check individual
fields where I wouldn’t expect duplicates (primary keys and potentially
other fields).

1.  anime\_demo\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| demo\_id | int  |             |

``` r
get_dupes(anime_genres_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, genres_id

    ## [1] tm_ky      mal_id     genres_id  dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_genres_table, mal_id, genres_id)
```

    ## No duplicate combinations found of: mal_id, genres_id

    ## [1] mal_id     genres_id  dupe_count tm_ky     
    ## <0 rows> (or 0-length row.names)

2.  anime\_genres\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| genres\_id | int  | PK          |

``` r
get_dupes(anime_genres_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, genres_id

    ## [1] tm_ky      mal_id     genres_id  dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_genres_table, mal_id, genres_id)
```

    ## No duplicate combinations found of: mal_id, genres_id

    ## [1] mal_id     genres_id  dupe_count tm_ky     
    ## <0 rows> (or 0-length row.names)

3.  anime\_ranking\_table

| Field                          | Type | Primary Key |
|:-------------------------------|:-----|:------------|
| tm\_ky                         | int  | PK          |
| mal\_id                        | int  | PK          |
| mean                           | dbl  |             |
| rank                           | int  |             |
| popularity                     | int  |             |
| num\_scoring\_users            | int  |             |
| statistics.watching            | int  |             |
| statistics.completed           | int  |             |
| statistics.on\_hold            | int  |             |
| statistics.dropped             | int  |             |
| statistics.plan\_to\_watch     | int  |             |
| statistics.num\_scoring\_users | int  |             |

``` r
get_dupes(anime_ranking_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, mean, rank, popularity, num_scoring_users, statistics.watching, statistics.completed, statistics.on_hold, ... and 3 other variables

    ##  [1] tm_ky                        mal_id                      
    ##  [3] mean                         rank                        
    ##  [5] popularity                   num_scoring_users           
    ##  [7] statistics.watching          statistics.completed        
    ##  [9] statistics.on_hold           statistics.dropped          
    ## [11] statistics.plan_to_watch     statistics.num_scoring_users
    ## [13] dupe_count                  
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_ranking_table, mal_id)
```

    ## No duplicate combinations found of: mal_id

    ##  [1] mal_id                       dupe_count                  
    ##  [3] tm_ky                        mean                        
    ##  [5] rank                         popularity                  
    ##  [7] num_scoring_users            statistics.watching         
    ##  [9] statistics.completed         statistics.on_hold          
    ## [11] statistics.dropped           statistics.plan_to_watch    
    ## [13] statistics.num_scoring_users
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_ranking_table, rank)
```

    ## No duplicate combinations found of: rank

    ##  [1] rank                         dupe_count                  
    ##  [3] tm_ky                        mal_id                      
    ##  [5] mean                         popularity                  
    ##  [7] num_scoring_users            statistics.watching         
    ##  [9] statistics.completed         statistics.on_hold          
    ## [11] statistics.dropped           statistics.plan_to_watch    
    ## [13] statistics.num_scoring_users
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_ranking_table, popularity)
```

    ## No duplicate combinations found of: popularity

    ##  [1] popularity                   dupe_count                  
    ##  [3] tm_ky                        mal_id                      
    ##  [5] mean                         rank                        
    ##  [7] num_scoring_users            statistics.watching         
    ##  [9] statistics.completed         statistics.on_hold          
    ## [11] statistics.dropped           statistics.plan_to_watch    
    ## [13] statistics.num_scoring_users
    ## <0 rows> (or 0-length row.names)

There can be duplicates for both rank and popularity (even though they
*should* be unique. The data download occurs over the space of \~1 hour.
Because of this, rankings may change slightly while the download is
occuring resulting in duplicates or gaps.

Since I am not including the popularity or rank as items in my download,
this is ok, **however, if anyone is using these fields it should be a
caveat**.

4.  anime\_studios\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| studio\_id | int  | PK          |

``` r
get_dupes(anime_studios_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, studio_id

    ## [1] tm_ky      mal_id     studio_id  dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_studios_table, mal_id, studio_id)
```

    ## No duplicate combinations found of: mal_id, studio_id

    ## [1] mal_id     studio_id  dupe_count tm_ky     
    ## <0 rows> (or 0-length row.names)

5.  anime\_syn\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| synonyms | chr  |             |

``` r
get_dupes(anime_syn_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, synonyms

    ## [1] tm_ky      mal_id     synonyms   dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_syn_table, mal_id, synonyms)
```

    ## No duplicate combinations found of: mal_id, synonyms

    ## [1] mal_id     synonyms   dupe_count tm_ky     
    ## <0 rows> (or 0-length row.names)

Interestingly, in some cases, there can be duplicates of the synonyms.
These are present on the website, however, I can exclude them here

``` r
anime_syn_table <- anime_syn_table %>%
  distinct(tm_ky, mal_id, synonyms)
```

6.  anime\_table

| Field                  | Type | Primary Key |
|:-----------------------|:-----|:------------|
| tm\_ky                 | int  | PK          |
| mal\_id                | int  | PK          |
| title                  | chr  |             |
| main\_picture.medium   | chr  |             |
| main\_picture.large    | chr  |             |
| alternative\_titles.en | chr  |             |
| alternative\_titles.ja | chr  |             |
| start\_date            | chr  |             |
| end\_date              | chr  |             |
| synopsis               | chr  |             |
| media\_type            | chr  |             |
| status                 | chr  |             |
| num\_episodes          | int  |             |
| start\_season.year     | int  |             |
| start\_season.season   | chr  |             |
| rating                 | chr  |             |
| nsfw                   | chr  |             |
| demo\_de               | chr  |             |
| genres\_de             | chr  |             |
| studios\_de            | chr  |             |
| synonyms               | chr  |             |
| alternative\_title     | chr  |             |

``` r
get_dupes(anime_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, title, main_picture.medium, main_picture.large, alternative_titles.en, alternative_titles.ja, start_date, end_date, ... and 13 other variables

    ##  [1] tm_ky                 mal_id                title                
    ##  [4] main_picture.medium   main_picture.large    alternative_titles.en
    ##  [7] alternative_titles.ja start_date            end_date             
    ## [10] synopsis              media_type            status               
    ## [13] num_episodes          start_season.year     start_season.season  
    ## [16] rating                nsfw                  demo_de              
    ## [19] genres_de             studios_de            synonyms             
    ## [22] alternative_title     dupe_count           
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(anime_table, mal_id)
```

    ## No duplicate combinations found of: mal_id

    ##  [1] mal_id                dupe_count            tm_ky                
    ##  [4] title                 main_picture.medium   main_picture.large   
    ##  [7] alternative_titles.en alternative_titles.ja start_date           
    ## [10] end_date              synopsis              media_type           
    ## [13] status                num_episodes          start_season.year    
    ## [16] start_season.season   rating                nsfw                 
    ## [19] demo_de               genres_de             studios_de           
    ## [22] synonyms              alternative_title    
    ## <0 rows> (or 0-length row.names)

7.  rank\_table

| Field          | Type | Primary Key |
|:---------------|:-----|:------------|
| tm\_ky         | int  | PK          |
| mal\_id        | int  | PK          |
| title          | chr  |             |
| rank           | int  |             |
| rank\_category | chr  | PK          |

``` r
get_dupes(rank_table)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, mal_id, title, rank, rank_category

    ## [1] tm_ky         mal_id        title         rank          rank_category
    ## [6] dupe_count   
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(rank_table, mal_id, rank_category)
```

    ##   mal_id rank_category dupe_count tm_ky        title rank
    ## 1  11079      favorite          2     2 Kill Me Baby 1500
    ## 2  11079      favorite          2     2 Kill Me Baby 1501

``` r
get_dupes(rank_table, mal_id, rank_category)
```

    ##   mal_id rank_category dupe_count tm_ky        title rank
    ## 1  11079      favorite          2     2 Kill Me Baby 1500
    ## 2  11079      favorite          2     2 Kill Me Baby 1501

``` r
get_dupes(rank_table, rank_category, rank)
```

    ## No duplicate combinations found of: rank_category, rank

    ## [1] rank_category rank          dupe_count    tm_ky         mal_id       
    ## [6] title        
    ## <0 rows> (or 0-length row.names)

Interestingly, there are no duplicates for rank. I assume that because
this download is pretty fast, there isn’t time for dynamic changes in
rank as time passes.

8.  demo\_l

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| demo\_id | int  | PK          |
| demo\_de | chr  |             |

``` r
get_dupes(demo_l)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, demo_id, demo_de

    ## [1] tm_ky      demo_id    demo_de    dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(demo_l, demo_id)
```

    ## No duplicate combinations found of: demo_id

    ## [1] demo_id    dupe_count tm_ky      demo_de   
    ## <0 rows> (or 0-length row.names)

9.  genres\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| genres\_id | int  | PK          |
| genres\_de | chr  |             |

``` r
get_dupes(genres_l)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, genres_id, genres_de

    ## [1] tm_ky      genres_id  genres_de  dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(genres_l, genres_id)
```

    ## No duplicate combinations found of: genres_id

    ## [1] genres_id  dupe_count tm_ky      genres_de 
    ## <0 rows> (or 0-length row.names)

10. studios\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| studio\_id | int  | PK          |
| studio\_de | chr  |             |

``` r
get_dupes(studios_l)
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: tm_ky, studio_id, studio_de

    ## [1] tm_ky      studio_id  studio_de  dupe_count
    ## <0 rows> (or 0-length row.names)

``` r
get_dupes(studios_l, studio_id)
```

    ## No duplicate combinations found of: studio_id

    ## [1] studio_id  dupe_count tm_ky      studio_de 
    ## <0 rows> (or 0-length row.names)

### Check for inconsistencies in names

There are only a few fields that I can review for consistency. While I
might want to ensure that every title doesn’t have a slightly different
name, it just isn’t feasible at this point. As a result, I will take a
focused approach to each table

1.  anime\_demo\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| demo\_id | int  |             |

``` r
tabyl(anime_demo_table, demo_id)
```

    ##  demo_id   n     percent
    ##       15   2 0.008333333
    ##       25  29 0.120833333
    ##       27 138 0.575000000
    ##       42  58 0.241666667
    ##       43  13 0.054166667

2.  anime\_genres\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| genres\_id | int  | PK          |

``` r
tabyl(anime_genres_table, genres_id)
```

3.  anime\_ranking\_table

| Field                          | Type | Primary Key |
|:-------------------------------|:-----|:------------|
| tm\_ky                         | int  | PK          |
| mal\_id                        | int  | PK          |
| mean                           | dbl  |             |
| rank                           | int  |             |
| popularity                     | int  |             |
| num\_scoring\_users            | int  |             |
| statistics.watching            | int  |             |
| statistics.completed           | int  |             |
| statistics.on\_hold            | int  |             |
| statistics.dropped             | int  |             |
| statistics.plan\_to\_watch     | int  |             |
| statistics.num\_scoring\_users | int  |             |

4.  anime\_studios\_table

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| mal\_id    | int  | PK          |
| studio\_id | int  | PK          |

``` r
tabyl(anime_studios_table, studio_id)
```

5.  anime\_syn\_table

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| mal\_id  | int  | PK          |
| synonyms | chr  |             |

6.  anime\_table

| Field                  | Type | Primary Key |
|:-----------------------|:-----|:------------|
| tm\_ky                 | int  | PK          |
| mal\_id                | int  | PK          |
| title                  | chr  |             |
| main\_picture.medium   | chr  |             |
| main\_picture.large    | chr  |             |
| alternative\_titles.en | chr  |             |
| alternative\_titles.ja | chr  |             |
| start\_date            | chr  |             |
| end\_date              | chr  |             |
| synopsis               | chr  |             |
| media\_type            | chr  |             |
| status                 | chr  |             |
| num\_episodes          | int  |             |
| start\_season.year     | int  |             |
| start\_season.season   | chr  |             |
| rating                 | chr  |             |
| nsfw                   | chr  |             |
| demo\_de               | chr  |             |
| genres\_de             | chr  |             |
| studios\_de            | chr  |             |
| synonyms               | chr  |             |
| alternative\_title     | chr  |             |

``` r
tabyl(anime_table, media_type)
```

    ##  media_type   n percent
    ##          tv 393       1

``` r
tabyl(anime_table, status)
```

    ##            status   n    percent
    ##  currently_airing   9 0.02290076
    ##   finished_airing 384 0.97709924

``` r
tabyl(anime_table, start_season.year)
```

    ##  start_season.year  n     percent
    ##               1970  1 0.002544529
    ##               1978  2 0.005089059
    ##               1979  1 0.002544529
    ##               1980  1 0.002544529
    ##               1982  1 0.002544529
    ##               1984  1 0.002544529
    ##               1985  1 0.002544529
    ##               1986  2 0.005089059
    ##               1987  1 0.002544529
    ##               1988  1 0.002544529
    ##               1989  1 0.002544529
    ##               1990  1 0.002544529
    ##               1992  1 0.002544529
    ##               1993  1 0.002544529
    ##               1995  2 0.005089059
    ##               1996  6 0.015267176
    ##               1997  2 0.005089059
    ##               1998  5 0.012722646
    ##               1999  4 0.010178117
    ##               2000  1 0.002544529
    ##               2001  1 0.002544529
    ##               2002  8 0.020356234
    ##               2003  5 0.012722646
    ##               2004  7 0.017811705
    ##               2005  8 0.020356234
    ##               2006 18 0.045801527
    ##               2007 17 0.043256997
    ##               2008 13 0.033078880
    ##               2009 14 0.035623410
    ##               2010 13 0.033078880
    ##               2011 19 0.048346056
    ##               2012 22 0.055979644
    ##               2013 18 0.045801527
    ##               2014 21 0.053435115
    ##               2015 27 0.068702290
    ##               2016 20 0.050890585
    ##               2017 20 0.050890585
    ##               2018 22 0.055979644
    ##               2019 23 0.058524173
    ##               2020 21 0.053435115
    ##               2021 34 0.086513995
    ##               2022  6 0.015267176

Surprisingly, the number of anime decreased after 2018 and 2019, but
increased a bit in 2021.

``` r
tabyl(anime_table, start_season.season)
```

    ##  start_season.season   n   percent
    ##                 fall 131 0.3333333
    ##               spring 123 0.3129771
    ##               summer  57 0.1450382
    ##               winter  82 0.2086514

``` r
tabyl(anime_table, rating)
```

    ##  rating   n     percent
    ##       g   7 0.017811705
    ##      pg   1 0.002544529
    ##   pg_13 269 0.684478372
    ##       r 116 0.295165394

``` r
tabyl(anime_table, nsfw)
```

    ##   nsfw   n     percent
    ##   gray   1 0.002544529
    ##  white 392 0.997455471

7.  rank\_table

| Field          | Type | Primary Key |
|:---------------|:-----|:------------|
| tm\_ky         | int  | PK          |
| mal\_id        | int  | PK          |
| title          | chr  |             |
| rank           | int  |             |
| rank\_category | chr  | PK          |

``` r
tabyl(rank_table, rank_category)
```

    ##  rank_category     n     percent
    ##         airing   298 0.003804126
    ##            all 19380 0.247395833
    ##   bypopularity 19380 0.247395833
    ##       favorite 23515 0.300181270
    ##          movie  3501 0.044692096
    ##            ova  3944 0.050347222
    ##        special  2313 0.029526654
    ##             tv  5583 0.071269914
    ##       upcoming   422 0.005387051

8.  demo\_l

| Field    | Type | Primary Key |
|:---------|:-----|:------------|
| tm\_ky   | int  | PK          |
| demo\_id | int  | PK          |
| demo\_de | chr  |             |

``` r
tabyl(demo_l, demo_de)
```

    ##  demo_de n percent
    ##    Josei 1     0.2
    ##     Kids 1     0.2
    ##   Seinen 1     0.2
    ##   Shoujo 1     0.2
    ##  Shounen 1     0.2

9.  genres\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| genres\_id | int  | PK          |
| genres\_de | chr  |             |

``` r
tabyl(genres_l, genres_de)
```

    ##      genres_de n    percent
    ##         Action 1 0.02857143
    ##      Adventure 1 0.02857143
    ##    Avant Garde 1 0.02857143
    ##      Boys Love 1 0.02857143
    ##           Cars 1 0.02857143
    ##         Comedy 1 0.02857143
    ##         Demons 1 0.02857143
    ##          Drama 1 0.02857143
    ##          Ecchi 1 0.02857143
    ##        Fantasy 1 0.02857143
    ##           Game 1 0.02857143
    ##     Girls Love 1 0.02857143
    ##        Gourmet 1 0.02857143
    ##          Harem 1 0.02857143
    ##     Historical 1 0.02857143
    ##         Horror 1 0.02857143
    ##   Martial Arts 1 0.02857143
    ##          Mecha 1 0.02857143
    ##       Military 1 0.02857143
    ##          Music 1 0.02857143
    ##        Mystery 1 0.02857143
    ##         Parody 1 0.02857143
    ##         Police 1 0.02857143
    ##  Psychological 1 0.02857143
    ##        Romance 1 0.02857143
    ##        Samurai 1 0.02857143
    ##         School 1 0.02857143
    ##         Sci-Fi 1 0.02857143
    ##  Slice of Life 1 0.02857143
    ##          Space 1 0.02857143
    ##         Sports 1 0.02857143
    ##    Super Power 1 0.02857143
    ##   Supernatural 1 0.02857143
    ##       Suspense 1 0.02857143
    ##        Vampire 1 0.02857143

10. studios\_l

| Field      | Type | Primary Key |
|:-----------|:-----|:------------|
| tm\_ky     | int  | PK          |
| studio\_id | int  | PK          |
| studio\_de | chr  |             |

``` r
tabyl(studios_l, studio_de)
```

    ##                 studio_de n    percent
    ##                      8bit 1 0.01219512
    ##              A-1 Pictures 1 0.01219512
    ##                  A.C.G.T. 1 0.01219512
    ##                   Ajia-Do 1 0.01219512
    ##                   Artland 1 0.01219512
    ##     Bandai Namco Pictures 1 0.01219512
    ##                 Bee Train 1 0.01219512
    ##  Bibury Animation Studios 1 0.01219512
    ##                     Bones 1 0.01219512
    ##              Brain's Base 1 0.01219512
    ##                 C-Station 1 0.01219512
    ##               CloverWorks 1 0.01219512
    ##          David Production 1 0.01219512
    ##                 Doga Kobo 1 0.01219512
    ##                 E&G Films 1 0.01219512
    ##                  Egg Firm 1 0.01219512
    ##           Encourage Films 1 0.01219512
    ##                     feel. 1 0.01219512
    ##                    Gainax 1 0.01219512
    ##                    Gallop 1 0.01219512
    ##               Geno Studio 1 0.01219512
    ##                     Gonzo 1 0.01219512
    ##                 Group TAC 1 0.01219512
    ##            Hal Film Maker 1 0.01219512
    ##     Hoods Drifters Studio 1 0.01219512
    ##                    Imagin 1 0.01219512
    ##                 J.C.Staff 1 0.01219512
    ##             Kinema Citrus 1 0.01219512
    ##           Kyoto Animation 1 0.01219512
    ##                    Lerche 1 0.01219512
    ##                LIDENFILMS 1 0.01219512
    ##                  Madhouse 1 0.01219512
    ##                  Manglobe 1 0.01219512
    ##                     MAPPA 1 0.01219512
    ##                Marvy Jack 1 0.01219512
    ##          Mushi Production 1 0.01219512
    ##          Nippon Animation 1 0.01219512
    ##                     Nomad 1 0.01219512
    ##                       Nut 1 0.01219512
    ##                       OLM 1 0.01219512
    ##                    Orange 1 0.01219512
    ##                P.A. Works 1 0.01219512
    ##                  P.I.C.S. 1 0.01219512
    ##                    Pastel 1 0.01219512
    ##              Pierrot Plus 1 0.01219512
    ##           Platinum Vision 1 0.01219512
    ##            Production I.G 1 0.01219512
    ##                     Radix 1 0.01219512
    ##                  SANZIGEN 1 0.01219512
    ##                 Satelight 1 0.01219512
    ##              Science SARU 1 0.01219512
    ##                Seven Arcs 1 0.01219512
    ##                     Shaft 1 0.01219512
    ##         Shin-Ei Animation 1 0.01219512
    ##                     Shuka 1 0.01219512
    ##              SILVER LINK. 1 0.01219512
    ##               Studio Bind 1 0.01219512
    ##              Studio Comet 1 0.01219512
    ##               Studio Deen 1 0.01219512
    ##             Studio Hibari 1 0.01219512
    ##              Studio Junio 1 0.01219512
    ##                Studio Kai 1 0.01219512
    ##            Studio Pierrot 1 0.01219512
    ##           Studio Signpost 1 0.01219512
    ##                   Sunrise 1 0.01219512
    ##                 SynergySP 1 0.01219512
    ##      Tatsunoko Production 1 0.01219512
    ##    Telecom Animation Film 1 0.01219512
    ##                Telescreen 1 0.01219512
    ##        Tezuka Productions 1 0.01219512
    ##         TMS Entertainment 1 0.01219512
    ##            Toei Animation 1 0.01219512
    ##       Tokyo Movie Shinsha 1 0.01219512
    ##                Trans Arts 1 0.01219512
    ##            Triangle Staff 1 0.01219512
    ##                   Trigger 1 0.01219512
    ##                    TROYCA 1 0.01219512
    ##            TYO Animations 1 0.01219512
    ##                  ufotable 1 0.01219512
    ##                 White Fox 1 0.01219512
    ##                Wit Studio 1 0.01219512
    ##                    Zero-G 1 0.01219512

### Exporting the final files

1.  anime\_demo\_table
2.  anime\_genres\_table
3.  anime\_ranking\_table
4.  anime\_studios\_table
5.  anime\_syn\_table
6.  anime\_table
7.  rank\_table
8.  demo\_l
9.  genres\_l
10. studios\_l
11. tm\_ky

``` r
exportFile <- function(file_name, df){
  if (file_test("-f", file_name)) {
    write.table(df, file = file_name, sep = ",", append = TRUE, quote = TRUE, row.names = FALSE, col.names = FALSE)
  } else {
    write.csv(df, file_name, row.names = FALSE)  
  }
}

exportFile(paste0("anime_demo_table",".csv"),anime_demo_table)
exportFile(paste0("anime_genres_table",".csv"),anime_genres_table)
exportFile(paste0("anime_ranking_table",".csv"),anime_ranking_table)
exportFile(paste0("anime_studios_table",".csv"),anime_studios_table)
exportFile(paste0("anime_syn_table",".csv"),anime_syn_table)
exportFile(paste0("anime_table",".csv"),anime_table)
exportFile(paste0("rank_table",".csv"),rank_table)
exportFile(paste0("demo_l",".csv"),demo_l)
exportFile(paste0("genres_l",".csv"),genres_l)
exportFile(paste0("studios_l",".csv"),studios_l)
exportFile(paste0("tm_ky",".csv"),tm_ky)
```

# Conclusion

At this point, all of the files have been written/appended to the
tables. Now they must be uploaded to the Tableau Public server and
Kaggle.com [here](https://www.kaggle.com/patmendoza/myanimelist-api)
