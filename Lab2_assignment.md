Lab2\_assignment
================
Matt Harris & Johnathan Clementi
MUSA 508, 9/13/2021

``` r
library(tidyverse)
library(tidycensus)
library(sf)
library(tmap) # mapping, install if you don't have it
set.seed(717)
```

This assignment if for you to complete a short version of the lab notes,
but you have to complete a number of the steps yourself. You will then
knit this to a markdown (not an HTML) and push it to your GitHub repo.
Unlike HTML, the RMarkdown knit to `github_document` can be viewed
directly on GitHub. You will them email your lab instructor with a link
to your repo.

Steps in this assignment:

1.  Make sure you have successfully read, run, and learned from the
    `MUSA_508_Lab2_sf.Rmd` Rmarkdown

2.  Find two new variables from the 2019 ACS data to load. Use `vars <-
    load_variables(2019, "acs5")` and `View(vars)` to see all of the
    variable from that ACS. Note that you should not pick something
    really obscure like count\_38yo\_cabinetmakers because you will get
    lots of NAs.

3.  Pick a neighborhood of the City to map. You will need to do some
    googling to figure this out. Use the [PHL Track
    Explorer](https://data-phl.opendata.arcgis.com/datasets/census-tracts-2010/explore?location=40.002759%2C-75.119097%2C11.91)
    to get the `GEOID10` number from each parcel and add them to the
    `myTracts` object below. This is just like what was done in the
    exercise, but with a different neighborhood of your choice. Remember
    that all GEOIDs need to be 10-characters long.

4.  In the first code chunk you will do that above and then edit the
    call-outs in the dplyr pipe sequence to `rename` and `mutate` your
    data.

5.  You will transform the data to `WGS84` by adding the correct EPSG
    code. This is discussed heavily in the exercise.

6.  You will produce a map of one of the variables you picked and
    highlight the neighborhood you picked. There are call-out within the
    `ggplot` code for you to edit.

7.  You can run the code chunks and lines of code as you edit to make
    sure everything works.

8.  Once you are done, hit the `knit` button at the top of the script
    window (little blue knitting ball) and you will see the output. Once
    it is what you want…

9.  Use the `Git` tab on the bottom left of right (depending on hour
    your Rstudio is laid out) and click the check box to `stage` all of
    your changes, write a commit note, hit the `commit` button, and then
    the `Push` button to push it to Github.

10. Check your Github repo to see you work in the cloud.

11. Email your lab instructor with a link\!

12. Congrats\! You made a map in code\!

## Load data from {tidycensus}

``` r
acs_vars <- c("B01001_001E", # ACS total Pop estimate
              "B25002_001E", # Estimate of total housing units
              "B25002_003E", # Number of vacant housing units
              "B19013_001E", # Median HH Income ($)
              "B02001_002E", # People describing themselves as "white alone"
              "B06009_006E", # Total graduate or professional degree)
              "B06009_002E", # Total less than high school graduate
              "B06009_004E", # Total some college or associates degree
              "B06009_005E") # Total bachelors degree

myTracts <- c("42101020800", 
              "42101020600", 
              "42101020700")

acsTractsPHL.2019.sf <- get_acs(geography = "tract",
                             year = 2019,
                             variables = acs_vars,
                             geometry = TRUE,
                             state  = "PA",
                             county = "Philadelphia",
                             output = "wide") %>%
  dplyr::select (GEOID, NAME, all_of(acs_vars)) %>%
  rename (total_pop.2019 = B01001_001E,
          total_HU.2019 = B25002_001E,
          total_vacant.2019 = B25002_003E,
          med_HH_Income.2019 = B19013_001E,
          total_White.2019 = B02001_002E,
          total_GradDeg.2019 = B06009_006E,
          total_LessHSGrad.2019 = B06009_002E,
          total_SomeCollege.2019 = B06009_004E,
          total_BachelorsDeg.2019 = B06009_005E) %>%
  mutate(Neighborhood = ifelse(GEOID %in% myTracts,
                               "EAST FALLS",
                               "REST OF PHILADELPHIA")) %>%
  mutate(pctLessHS = total_LessHSGrad.2019/total_pop.2019,
         pctSomeCollege = total_SomeCollege.2019/total_pop.2019,
         pctBachelors = total_BachelorsDeg.2019/total_pop.2019)
```

## Transform to WGS84 with {sf}

``` r
acsTractsPHL.2019.sf <- acsTractsPHL.2019.sf %>% 
  st_transform(crs = "EPSG:4326")
```

## Plot with {ggplot2}

    ## although coordinates are longitude/latitude, st_union assumes that they are planar

![](Lab2_assignment_files/figure-gfm/ggplot_geom_sf1-1.png)<!-- -->

    ## although coordinates are longitude/latitude, st_union assumes that they are planar

![](Lab2_assignment_files/figure-gfm/ggplot_geom_sf2-1.png)<!-- -->

    ## although coordinates are longitude/latitude, st_union assumes that they are planar

![](Lab2_assignment_files/figure-gfm/ggplot_geom_sf3-1.png)<!-- -->