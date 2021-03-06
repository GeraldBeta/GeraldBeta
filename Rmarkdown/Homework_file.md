---
title: "GG Plot 111 Report"
author: "Gerald Beta"
date: "01/11/2021"
output:
  html_document:
    keep_md: True
    code_folding: "hide"
---




## Report overview


This report demonstrates how ggplot can be used to generate different plots using different approaches. GGplot is a widely known  and used package in the R community. It makes life easy and giving out awesome plots.


We used the following packages to complete the work:

library(tidyverse)

library(janitor)

library(patchwork)

library(viridis)

library(ggrepel)

library(ggridges)

library(here)


## Installing packages

Packages are installed using the "install.package" function and loaded using the "library".







## Adding customized theme

We add our customized theme saved as a function by using the 


```r
source(here("functions", "theme_gerry.R"))        ## adding my theme
```



## Adding/ reading data into R



```r
chlorophyll_original <- read_csv("data/chlorophyll_a.csv")
```

```
## Rows: 135 Columns: 16
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (4): lake_name, sampling_date, gps_coordinates_utm, notes
## dbl (12): latitude, longitude, sample_depth_m, chlorophyll_concentration_ug/...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
phosphorus_original <- read_csv("data/phosphorus_profiles.csv")     #adding data into r
```

```
## Rows: 110 Columns: 9
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (3): sampling_date, lake_name, sampling_location_utm
## dbl (6): latitude, longitude, sample_depth_m, total_phosphorus_mg/L, total_r...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


## Data cleaning 


We clean data using the clean_names function. It removes errors, capital letter, space/gaps and makes data tidy



```r
chlorophyll <- chlorophyll_original %>% clean_names()
chlorophyll
```

```
## # A tibble: 135 x 16
##    lake_name sampling_date gps_coordinates_utm latitude longitude sample_depth_m
##    <chr>     <chr>         <chr>                  <dbl>     <dbl>          <dbl>
##  1 Devonian  2016-Jan-14   14U 0627523 5520102     49.8     -97.2              0
##  2 Lake 3    2016-Jan-14   14U 0627538 5519851     49.8     -97.2              0
##  3 Lake 2    2016-Jan-14   14U 0627580 5519673     49.8     -97.2              0
##  4 Muir      2016-Jan-19   14U 0627940 5519722     49.8     -97.2              0
##  5 Cargill   2016-Jan-19   14U 0628021 5520791     49.8     -97.2              0
##  6 Devonian  2016-Feb-11   14U 0627523 5520102     49.8     -97.2              0
##  7 Lake 3    2016-Feb-11   14U 0627538 5519851     49.8     -97.2              0
##  8 Lake 2    2016-Feb-11   14U 0627580 5519673     49.8     -97.2              0
##  9 Muir      2016-Feb-11   14U 0627940 5519722     49.8     -97.2              0
## 10 Cargill   2016-Feb-11   14U 0628021 5520791     49.8     -97.2              0
## # ... with 125 more rows, and 10 more variables:
## #   chlorophyll_concentration_ug_l <dbl>, pheophytin_concentration_ug_l <dbl>,
## #   total_chlorophyll_pheophytin_ug_l <dbl>, sample_volume_l <dbl>,
## #   percent_filtered <dbl>, chlorophyll_665nm_absorbancy <dbl>,
## #   chlorophyll_750nm_absorbancy <dbl>, pheophytin_665nm_absorbancy <dbl>,
## #   pheophytin_750nm_absorbancy <dbl>, notes <chr>
```

```r
phosphorus <- phosphorus_original %>% clean_names()        ## cleaning data
phosphorus
```

```
## # A tibble: 110 x 9
##    sampling_date lake_name sampling_location_utm latitude longitude sample_depth_m
##    <chr>         <chr>     <chr>                    <dbl>     <dbl>          <dbl>
##  1 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              0
##  2 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              1
##  3 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              2
##  4 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              3
##  5 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              4
##  6 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              5
##  7 2017-Aug-04   Devonian  14U 0627523 5520102       49.8     -97.2              6
##  8 2017-Aug-04   Lake 2    14U 0627580 5519673       49.8     -97.2              0
##  9 2017-Aug-04   Lake 2    14U 0627580 5519673       49.8     -97.2              1
## 10 2017-Aug-04   Lake 2    14U 0627580 5519673       49.8     -97.2              2
## # ... with 100 more rows, and 3 more variables: total_phosphorus_mg_l <dbl>,
## #   total_reactive_phosphorus_mg_l <dbl>, ammonia_total_as_n_mg_l <dbl>
```


## Selecting data colums and Changing data to factor



We can select data column we are interested in using the dplyr :: select function or by simply filtering the data using filter function. Here we used dply select for the assignment. For future plotting and combining data sets from different data, it is important to convert your data colum into factors using the mutate(as.factor) function. We then group the data according to how we need to plot or analyse our data. We used geom jitter to plot our points. It allows us to visualize or edit our color balance. Not forgetting the scale_colour_viridis which smoother our plot colors that it to carter for color blind personals. We used geom_violin which is another form of boxplot but it shows a glimpse of how our data distribution. Lab function was used to remove axis labels and adding expression (suitable for statistics)


Chlorophyll data



```r
chlorophyll_data <- chlorophyll %>% mutate(lake_name = as.factor(lake_name)) %>%      ## changing data into factors 
                          dplyr::select(lake_name, chlorophyll_concentration_ug_l, sampling_date) %>%   ## selecting column of interest
                          group_by(lake_name, chlorophyll_concentration_ug_l) %>%
                          
                          ggplot() +
                          geom_jitter(aes(x = lake_name, y = chlorophyll_concentration_ug_l, colour = lake_name), width = 0.1, alpha = 0.8) +
                          geom_violin(aes(x = lake_name, y = chlorophyll_concentration_ug_l, colour = lake_name), width = 0.9, alpha = 0.4, size = 0.5) + #3 other version of box plot   
                          coord_cartesian(ylim = c(8,1000)) +
                          labs(x = "", 
                          y = expression(chlorophyll~concentration~(mu*g~.~L^{-1})),   #adding formulas mathematics 
                              size = 3) + 
                          
                          scale_fill_viridis(discrete = TRUE) +                 ## for color blind people
                          theme_gerry() + theme(legend.position = "none")   ## removing legend on our plots
                          
                          
chlorophyll_data
```

![](Homework_file_files/figure-html/unnamed-chunk-4-1.png)<!-- -->



## Selecting data colums and Changing data to factor



We can select data column we are interested in using the dplyr :: select function or by simply filtering the data using filter function. Here we used dply select for the assignment. For future plotting and combining data sets from different data, it is important to convert your data colum into factors using the mutate(as.factor) function. We then group the data according to how we need to plot or analyse our data. We used geom jitter to plot our points. It allows us to visualize or edit our color balance. Not forgetting the scale_colour_viridis which smoother our plot colors that it to carter for color blind personals. We used geom_violin which is another form of boxplot but it shows a glimpse of how our data distribution. Lab function was used to remove axis labels and adding expression (suitable for statistics)

phosphorus data 1 using filter



```r
phosphorus_plot1 <- phosphorus %>% filter(sample_depth_m > 0) %>%
                arrange(desc(sample_depth_m)) %>%
                mutate(lake_name = as.factor(lake_name)) %>%
  
                ggplot() +
                geom_violin(aes(x = lake_name, y = sample_depth_m, colour = lake_name), alpha = 0.5, width = 0.2) +
                geom_jitter(aes(x = lake_name, y = sample_depth_m, colour = lake_name), width = 0.2) +
                     
                scale_fill_viridis(discrete = TRUE) +
                theme_gerry() + 
                theme(legend.position = "none") +
                labs(x = "",
                y = expression(total~phosphorus~(mu*g~.~L^{-1})))

phosphorus_plot1
```

![](Homework_file_files/figure-html/unnamed-chunk-5-1.png)<!-- -->



phosphorus data 2 using mutate




```r
phosphorus_data_2 <- phosphorus %>% mutate(lake_name = as.factor(lake_name)) %>%
            dplyr::select(lake_name, sampling_date, total_phosphorus_mg_l) %>%
            group_by(lake_name, sampling_date) %>%
            summarise(total_phosphorus_mg_l = min(total_phosphorus_mg_l, na.rm = TRUE)) %>%
            ggplot() +
            geom_jitter(aes(x = lake_name, y = total_phosphorus_mg_l, colour = lake_name), alpha = 0.9, width = 0.1) +
            geom_violin(aes(x = lake_name, y = total_phosphorus_mg_l, colour = lake_name),
                            alpha = 0.4, size = 0.5, width = 0.9) +
            coord_cartesian(ylim = c(0, 0.35), expand = FALSE) +
            labs(x = "",
            y = expression(total~phosphorus~(mu*g~.~L^{-1}))) +
            scale_fill_viridis(discrete = TRUE) +
            theme_gerry() +
            theme(legend.position = "none")
```

```
## `summarise()` has grouped output by 'lake_name'. You can override using the `.groups` argument.
```

```r
phosphorus_data_2
```

![](Homework_file_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

## Combining plots using patchwork package.


Patchwork allows you to combine or merge two or more plots in one plot. It is very useful especially if you are working with multiply data sets. It works almost similar to facet_wrap function but patchwork combines plots using different data sets, with facet wrap its only one data set showing different plots. Patchwork use the symbol + to combine data.

option 1


```r
final_plot_1 <- chlorophyll_data + phosphorus_plot1 + plot_layout(nrow = 2, widths = c(3.2)) +     ## using patchwork to + to combine data
                                        plot_annotation(tag_level = c("A", "B", size = 4)) + ## tagging our plots
                                        theme_gerry() +
                                        theme(legend.position = "none") 
                                        
                                  
final_plot_1
```

![](Homework_file_files/figure-html/combining plot 1-1.png)<!-- -->


option 2 



```r
final_plot_2 <- chlorophyll_data + phosphorus_data_2 + plot_layout(nrow = 2, widths = c(3.2)) +     ## using patchwork to + to combine data
                                        plot_annotation(tag_level = c("A", "B", size = 4)) + ## tagging our plots
                                        
                                        theme_gerry() +
                                        theme(legend.position = "none")
                                      
                                              
final_plot_2                                       
```

![](Homework_file_files/figure-html/unnamed-chunk-7-1.png)<!-- -->




Saving plots as pdf



```r
ggsave(here("figures", "final_plot_1.pdf"), final_plot_1, width = 190, height = 120, units = "mm")

ggsave(here("figures", "final_plot_2.pdf"), final_plot_1, width = 190, height = 120, units = "mm")
```



Happy coding !!
