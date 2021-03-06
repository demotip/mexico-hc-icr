Examine Pilot Data
================
Aaron Erlich
03/06/2019

## Inter Coder Reliability

I need to get the same 10 which were coded by everyone. As far as I can
tell, it appears that only **five** of the coders coded these. I believe
Dan used a different system.

``` r
pacman::p_load(tidyverse, irr, kableExtra)
icr <- read_csv("./data_raw/Big Data y Acceso a Info en México_June 3, 2019_13.43.csv")
unique_codes <- read_csv("./data_raw/all_coders_coded.csv")

#remove gardage rows
icr <- icr %>% slice(-(1:5))
                     
#table(icr)

#deal with person who coded from a differet location (uggh)
icr <- icr %>% mutate(unique_coder = 
                        case_when(IPAddress %in% c("134.48.232.19", "174.103.168.235") ~ "Person_1",
                                  IPAddress %in% "174.60.143.107 " ~ "Person_2",
                                  IPAddress %in% "189.146.112.34" ~ "Person_3",
                                  IPAddress %in% "200.56.56.9" ~ "Person_4",
                                  IPAddress %in% "189.210.57.88" ~ "Person_5",
                                  IPAddress %in% "67.71.216.6" ~ "Person_6",
                                  IPAddress %in% "158.143.29.226" ~ "Person_7")) 

icr <- mutate(icr, folio_id = ifelse(grepl("'", Q19),
                                    trimws(Q19),
                                    trimws(str_c("'", Q19))))
#get rid of Dan???
icr2 <- icr %>% filter(folio_id  %in% unique_codes$folio_id & unique_coder != "Person_7")

icr2 <- mutate(icr2, Q14 = factor(Q14, levels = c("Poca o nada", 
                                                  "Menos de la mitad", 
                                                  "Aproximadamente la mitad",
                                                  "La mayoría",
                                                  "Toda")))

#table(icr$unique_coder)


data_wide <- reshape2::dcast(icr2,  folio_id ~ unique_coder, value.var="Q14") %>%
  mutate_at(vars(contains("Person")),  function(x) factor(x, levels = c("Poca o nada", 
                                                  "Menos de la mitad", 
                                                  "Aproximadamente la mitad",
                                                  "La mayoría",
                                                  "Toda")))
```

Okay, now let’s look at agreement. At least for these c coders, it lokes
like, mainly for *Toda* folks are in agreement. For the middle
categories, it is challenging. If we condensed the categories it would
look better. I won’t do more until I figure how who the unique coders
are

``` r
ratings <- select(data_wide, folio_id, contains("Person")) 

ratings %>% kable(., escape = TRUE)
```

<table>

<thead>

<tr>

<th style="text-align:left;">

folio\_id

</th>

<th style="text-align:left;">

Person\_1

</th>

<th style="text-align:left;">

Person\_3

</th>

<th style="text-align:left;">

Person\_4

</th>

<th style="text-align:left;">

Person\_5

</th>

<th style="text-align:left;">

Person\_6

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

’0000700014312

</td>

<td style="text-align:left;">

Aproximadamente la mitad

</td>

<td style="text-align:left;">

Aproximadamente la mitad

</td>

<td style="text-align:left;">

Aproximadamente la mitad

</td>

<td style="text-align:left;">

Aproximadamente la mitad

</td>

<td style="text-align:left;">

La mayoría

</td>

</tr>

<tr>

<td style="text-align:left;">

’0000900153514

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

<td style="text-align:left;">

La mayoría

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

<tr>

<td style="text-align:left;">

’0001700170212

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Aproximadamente la mitad

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

<tr>

<td style="text-align:left;">

’0002000146408

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

<tr>

<td style="text-align:left;">

’0064100496813

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

NA

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Poca o nada

</td>

</tr>

<tr>

<td style="text-align:left;">

’0064101001113

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

<tr>

<td style="text-align:left;">

’0413100001103

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

NA

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

</tr>

<tr>

<td style="text-align:left;">

’1026500126612

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

<tr>

<td style="text-align:left;">

’1031500032012

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Poca o nada

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

</tr>

<tr>

<td style="text-align:left;">

’1610100114315

</td>

<td style="text-align:left;">

La mayoría

</td>

<td style="text-align:left;">

Menos de la mitad

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

<td style="text-align:left;">

Toda

</td>

</tr>

</tbody>

</table>

``` r
# %>%
#   column_spec(1:5, width = "10em")
```

It’s hard to get agreement out of all the coders of the 8, where we have
all observations we get 3 thare the same across the board.

``` r
agree(select(ratings, contains("Person")))
```

    ##  Percentage agreement (Tolerance=0)
    ## 
    ##  Subjects = 8 
    ##    Raters = 5 
    ##   %-agree = 37.5

This is not a sufficient Kripp alpha, but it is getting there.

``` r
kripp.alpha(t(select(ratings, contains("Person"))), method = "ordinal")
```

    ##  Krippendorff's alpha
    ## 
    ##  Subjects = 10 
    ##    Raters = 5 
    ##     alpha = 0.55
