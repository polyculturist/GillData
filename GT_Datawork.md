GT_Datawork
================
migs
2024-05-20

``` r
library(ggplot2)
library(magrittr)
library(readr)
library(RCurl)
library(tidyr)
```

    ## 
    ## Attaching package: 'tidyr'

    ## The following object is masked from 'package:RCurl':
    ## 
    ##     complete

    ## The following object is masked from 'package:magrittr':
    ## 
    ##     extract

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ purrr     1.0.2
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ lubridate 1.9.3     ✔ tibble    3.2.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ tidyr::complete()  masks RCurl::complete()
    ## ✖ tidyr::extract()   masks magrittr::extract()
    ## ✖ dplyr::filter()    masks stats::filter()
    ## ✖ dplyr::lag()       masks stats::lag()
    ## ✖ purrr::set_names() masks magrittr::set_names()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
#Timestamp vs. Water Content, color = Depth
ggplot(df, aes(x = Datetimes, y = WaterAmt, color = Depth.cm)) +
  geom_smooth(na.rm = T) + 
 # geom_point() +
  scale_y_continuous(limits = c(0.18, 0.45)) ##Removes negative values from calibration
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

![](GT_Datawork_files/figure-gfm/basic%20visualizations-1.png)<!-- -->

``` r
#Timestamp vs. Water Content, color = Treatment
ggplot(df, aes(x = Datetimes, y = WaterAmt, color = Treatment)) +
  geom_smooth(na.rm = T) + 
 # geom_point() +
  scale_y_continuous(limits = c(0.18, 0.45)) ##Removes negative values from calibration
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

![](GT_Datawork_files/figure-gfm/basic%20visualizations-2.png)<!-- -->

``` r
#Timestamp vs. Water Content, color = Fallow YN
ggplot(df, aes(x = Datetimes, y = WaterAmt, color = Fallow_YN)) +
  geom_smooth(na.rm = T) + 
  #geom_point() +
  scale_y_continuous(limits = c(0.18, 0.45)) ##Removes negative values from calibration
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

![](GT_Datawork_files/figure-gfm/basic%20visualizations-3.png)<!-- -->

``` r
##Analyzing Weather Data
#Aggregating Daily Rainfall
daily_rain <- Weather_Data %>% 
  mutate(Date = as.Date(Datetimes)) %>%  #Extract the date part
  group_by(Date) %>% 
  summarise(daily_rainfall = sum(Rain...in, na.rm = T))
  
#print(daily_rain)

ggplot(daily_rain, aes(x = Date, y = daily_rainfall)) +
  geom_line(color = "blue", na.rm = T) +
  geom_point(color = "blue", na.rm = T) +
  labs(title = "UC Gill Tract 2023-24 Daily Rainfall",
       x = "Date",
       y = "Daily Rainfall (in)") +
  theme_minimal()
```

![](GT_Datawork_files/figure-gfm/Weather%20Graphs-1.png)<!-- -->

``` r
#Daily Temp High and Low
daily_temps <- Weather_Data %>% 
  mutate(Date = as.Date(Datetimes)) %>%  #Extract the date part
  group_by(Date) %>% 
  summarise(
    MinTemp = min(Temp....F, na.rm = T),
    MaxTemp = max(Temp....F, na.rm = T)
  )
#print(daily_temps)

#Reshaping Max & Min Temp dataframe
long_temps <- daily_temps %>% 
  pivot_longer(cols = c(MinTemp, MaxTemp),
               names_to = "TempType",
               values_to = "Temp.F")

long_temps$Temp.F <- as.numeric(long_temps$Temp.F)
```

    ## Warning: NAs introduced by coercion

``` r
#str(long_temps)

ggplot(long_temps, aes(x = Date, y = Temp.F, color = TempType)) +
  geom_line(aes(group = TempType), na.rm = T) +
  geom_point(na.rm = T) + # Add points to the line plot for better visibility
  labs(title = "UC Gill Tract 2023-24 Daily Minimum and Maximum Temperatures",
       x = "Date",
       y = "Temperature (°F)") +
  scale_color_manual(values = c("MinTemp" = "blue", "MaxTemp" = "red"),
                     labels = c("Minimum Temperature", "Maximum Temperature")) +
  theme_minimal()
```

![](GT_Datawork_files/figure-gfm/Weather%20Graphs-2.png)<!-- -->

``` r
#1. What do the diff treatments look like in biomass? Is it significant? Does it represent greater N uptake?

#2. Can I use et models (combination, energy transfer) to distinguish between treatments?

#3. What does the drydown look like (post-April) for different treatments?

#4. Tradeoffs: Planting early/term early to minimize water loss and protect groundwater (N scavenging), BUT irrigation would be needed. Would the irrigated water 'budget' be returned?
```
