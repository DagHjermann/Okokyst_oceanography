Match ferrybox sample numbers to stations
================
DHJ
17 februar 2020

  - [1. Libraries](#libraries)
  - [2. Read AqM station data](#read-aqm-station-data)
  - [3. Ferrybox sample data 2018-2019](#ferrybox-sample-data-2018-2019)
      - [a. Ferrybox sample data 2019](#a.-ferrybox-sample-data-2019)
      - [b. Ferrybox sample data 2018](#b.-ferrybox-sample-data-2018)
      - [c. Combine data for both years for each
        ferry](#c.-combine-data-for-both-years-for-each-ferry)
      - [d. Save sample files](#d.-save-sample-files)
      - [e. Sample numbers used in
        files](#e.-sample-numbers-used-in-files)
  - [4. Match automatic samples](#match-automatic-samples)
      - [Set time and direction for
        Trollfjord](#set-time-and-direction-for-trollfjord)
      - [Prepare ferrybox samples data for
        mapping](#prepare-ferrybox-samples-data-for-mapping)
      - [Check Fantasy samples in map](#check-fantasy-samples-in-map)
      - [Check Trollfjord samples in
        map](#check-trollfjord-samples-in-map)
      - [Check stations in map](#check-stations-in-map)
      - [Stations vs Fantasy sample
        positions](#stations-vs-fantasy-sample-positions)
      - [Stations vs Trollfjord sample
        positions](#stations-vs-trollfjord-sample-positions)
      - [Check specific Trollfjord sample
        number](#check-specific-trollfjord-sample-number)
      - [Trollfjord: Get Ferrybox samples in the vicinity of specific
        lon,
        lat](#trollfjord-get-ferrybox-samples-in-the-vicinity-of-specific-lon-lat)
      - [Check VT80 + VT23 as example](#check-vt80-vt23-as-example)
  - [5. Trollfjord: Get closest station for each ferrybox data
    point](#trollfjord-get-closest-station-for-each-ferrybox-data-point)
      - [Make UTM coordinates](#make-utm-coordinates)
      - [Function](#function)
      - [Make distance matrix and find closest
        station](#make-distance-matrix-and-find-closest-station)
      - [Save
        ‘df\_ferrybox\_sampledata\_tf’](#save-df_ferrybox_sampledata_tf)
  - [6. Check positions of manual
    samples](#check-positions-of-manual-samples)
      - [Check samples in map - prepare
        data](#check-samples-in-map---prepare-data)
      - [Check Fantasy samples in map](#check-fantasy-samples-in-map-1)
      - [Check Trollfjord samples in
        map](#check-trollfjord-samples-in-map-1)

Find which sample numbers (in Ferrybox files) belongs to which stations

Corresponding numbers in Fantasy:  
\- VT4 = sample no 21-22

Often, but not always (\!), corresponding numbers in Trollfjord:  
\- VT4 = sample no 23-24  
\- VT72 = sample no 21-22  
\- VT23 = sample no 17-18  
\- VT80 = sample no 11-12  
\- VT45 = sample no 13-14  
\- VT22 = sample no 15-16  
\- VR25 = sample no 6-7  
\- VR23 = sample no 4-5  
\- VR76 = sample no 2-3  
For Trollfjord, we make a search for closest station as well (part 5
below)

Ferrybox sample files for 2018-2019 saved as  
\- “Datasett/21\_df\_ferrybox\_sampledata\_fa.rds”  
\- “Datasett/21\_df\_ferrybox\_sampledata\_tf.rds”  
\-
“Datasett/Ferrybox\_samples\_OneDrive/FA\_2018-19\_automatic\_samples.xlsx”  
\-
“Datasett/Ferrybox\_samples\_OneDrive/TF\_2018-19\_automatic\_samples.xlsx”

## 1\. Libraries

``` r
library(plyr)      # used by functions in "Get_files_NIVA_ftp_server_functions.R" 
library(dplyr)     # load AFTER plyr, so e,g, we use count() from dplyr
library(purrr)
library(ggplot2)
library(lubridate)
library(readxl)
library(tidyr)
library(RColorBrewer)
# For mapping:
library(mapview)
library(sf)

source("Get_files_NIVA_ftp_server_functions.R")

# library(niRvana)
#source("12_QA_2019_from_excel_functions.R")
# RColorBrewer::display.brewer.all()
```

## 2\. Read AqM station data

``` r
df_aqm_stations <- read_excel("Datasett/AqM_2017_2019_ØKOKYST_Ferrybox_ToR.xlsx", 
                              sheet = "StationPoint")
```

## 3\. Ferrybox sample data 2018-2019

### a. Ferrybox sample data 2019

We do this before 2018 in order to get the headers (column names), which
we will use for 2018 data  
\- Data downloaded from Pierre’s OneDrive 12.02.2020  
\- [Link to Pierre’s
OneDrive](https://niva365-my.sharepoint.com/:f:/g/personal/pierre_jaccard_niva_no/Et9g2CZL2_tEg3vOt7ACQh0BHCjrOcKzPz-iAgdYst2ddQ?e=hpkNeW)

``` r
df_ferrybox_sampledata_2019_fa <- readxl::read_excel(
  "Datasett/Ferrybox_samples_OneDrive/FA_2019_automatic_samples.xlsx")

df_ferrybox_sampledata_2019_tf <- readxl::read_excel(
  "Datasett/Ferrybox_samples_OneDrive/TF_2019_automatic_samples.xlsx")

# names(df_ferrybox_sampledata_2019_fa)
# names(df_ferrybox_sampledata_2019_tf)

X <- df_ferrybox_sampledata_2019_fa$SYSTEM_DATE_DMY - ymd_hms("1899-12-31 00:00:00")

df_ferrybox_sampledata_2019_fa <- df_ferrybox_sampledata_2019_fa %>%
  mutate(TIME = SYSTEM_TIME + X)

# Check
# df_samples_fa$SYSTEM_DATE_DMY %>% head()
# df_samples_fa$SYSTEM_TIME %>% head()
# df_samples_fa$TIME %>% head()

unique(df_ferrybox_sampledata_2019_fa$SYSTEM_DATE_DMY)
```

    ##  [1] "2018-12-31 UTC" "2019-01-03 UTC" "2019-01-04 UTC" "2019-02-18 UTC" "2019-02-19 UTC" "2019-02-26 UTC" "2019-02-27 UTC" "2019-03-06 UTC"
    ##  [9] "2019-03-07 UTC" "2019-03-20 UTC" "2019-03-21 UTC" "2019-04-09 UTC" "2019-04-10 UTC" "2019-04-12 UTC" "2019-04-23 UTC" "2019-04-24 UTC"
    ## [17] "2019-05-13 UTC" "2019-05-14 UTC" "2019-05-29 UTC" "2019-05-30 UTC" "2019-06-08 UTC" "2019-06-09 UTC" "2019-06-26 UTC" "2019-06-27 UTC"
    ## [25] "2019-07-01 UTC" "2019-07-14 UTC" "2019-07-15 UTC" "2019-07-30 UTC" "2019-07-31 UTC" "2019-08-15 UTC" "2019-08-16 UTC" "2019-09-02 UTC"
    ## [33] "2019-09-03 UTC" "2019-09-08 UTC" "2019-09-09 UTC" "2019-09-22 UTC" "2019-09-23 UTC" "2019-10-02 UTC" "2019-10-03 UTC" "2019-10-16 UTC"
    ## [41] "2019-10-17 UTC" "2019-10-28 UTC" "2019-10-29 UTC" "2019-11-17 UTC" "2019-11-18 UTC" "2019-11-27 UTC" "2019-11-28 UTC" "2019-12-09 UTC"
    ## [49] "2019-12-10 UTC" "2019-12-17 UTC" "2019-12-18 UTC"

### b. Ferrybox sample data 2018

``` r
# 4. Download all Ferrybox log data    
# Or just use saved log data

redownload_data <- FALSE  # if FALSE, we just read the saved data


if (redownload_data){  # takes just a couple of minutes
  
  #
  # a. Get dates and filenames ('df_samplefilenames_fa_2018')  
  #
  # get_folders()
  
  #
  # Fantasy
  #
  
  # Get the url for the vessel we want
  url.folder <- get_url_foldername("fantasy")
  
  # Get file names of all log files for that vessel
  samplefiles_fn <- get_filenames_samplefiles("fantasy")
  
  head(samplefiles_fn)
  # Check
  # tail(logfiles_fn)
  
  # Combine with dates 
  df_samplefilenames_fa_2018 <- data.frame(
    filename = samplefiles_fn,
    Date = ymd(substr(samplefiles_fn, 9, 16)),   # note that 9,16 is specifically for 'samples'
    stringsAsFactors = FALSE) %>%
    filter(year(Date) == 2018)
  
  
  #
  # Trollfjord
  #
  
  # Get the url for the vessel we want
  url.folder <- get_url_foldername("trollfjord")
  
  # Get file names of all log files for that vessel
  samplefiles_fn <- get_filenames_samplefiles("trollfjord")
  
  # Check
  # tail(logfiles_fn)
  
  # Combine with dates 
  df_samplefilenames_tf_2018 <- data.frame(
    filename = samplefiles_fn,
    Date = ymd(substr(samplefiles_fn, 9, 16)), # note that 9,16 is specifically for 'samples'
    stringsAsFactors = FALSE) %>%
    filter(year(Date) == 2018)
  
  #
  # b. Combine filenames  
  #
  
  df_samplefilenames_2018 <- 
    bind_rows(
      df_samplefilenames_fa_2018 %>% mutate(Ferrybox = "fantasy"),
      df_samplefilenames_tf_2018 %>% mutate(Ferrybox = "trollfjord")
    )
  
  #
  # c. Download sample file data
  #
  df_ferrybox_sampledata_2018_fa <- get_several_logfiles(
    df_samplefilenames_fa_2018$filename, 
    vessel = "fantasy",
    trace = FALSE)
  
  df_ferrybox_sampledata_2018_tf <- get_several_logfiles(
    df_samplefilenames_tf_2018$filename, 
    vessel = "trollfjord",
    trace = FALSE)
  
  names(df_ferrybox_sampledata_2018_fa) <- names(df_ferrybox_sampledata_2019_fa)
  names(df_ferrybox_sampledata_2018_tf) <- names(df_ferrybox_sampledata_2019_tf)

  #
  # d. Save
  #
  saveRDS(df_ferrybox_sampledata_2018_fa,
          "Datasett/21_df_ferrybox_sampledata_2018_fa.rds")
  saveRDS(df_ferrybox_sampledata_2018_tf,
          "Datasett/21_df_ferrybox_sampledata_2018_tf.rds")
  
  
} else {
  
  df_ferrybox_sampledata_2018_fa <-
    readRDS("Datasett/21_df_ferrybox_sampledata_2018_fa.rds")
  df_ferrybox_sampledata_2018_tf <-
    readRDS("Datasett/21_df_ferrybox_sampledata_2018_tf.rds")
  
}
```

### c. Combine data for both years for each ferry

  - 2018 + 2019  
  - The file columns differ betrween ferries, so we donæt combine them

<!-- end list -->

``` r
#
# Fantasy
#

# df_ferrybox_sampledata_2018_fa[1:7] %>% str()
# df_ferrybox_sampledata_2019_fa[1:7] %>% str()

df_ferrybox_sampledata_2018_fa <- df_ferrybox_sampledata_2018_fa %>%
  mutate(SYSTEM_DATE_DMY = dmy_hms(paste(SYSTEM_DATE_DMY, "00:00:00")),
         SYSTEM_TIME = ymd_hms(paste("1899-12-31", SYSTEM_TIME)))

df_ferrybox_sampledata_fa <- bind_rows(
  df_ferrybox_sampledata_2018_fa,
  df_ferrybox_sampledata_2019_fa)

#
# Trollfjord
#
# df_ferrybox_sampledata_2018_tf[1:7] %>% str()
# df_ferrybox_sampledata_2019_tf[1:7] %>% str()

df_ferrybox_sampledata_2018_tf <- df_ferrybox_sampledata_2018_tf %>%
  mutate(SYSTEM_DATE = dmy_hms(paste(SYSTEM_DATE, "00:00:00")),
         SYSTEM_TIME = ymd_hms(paste("1899-12-31", SYSTEM_TIME)))

df_ferrybox_sampledata_tf <- bind_rows(
  df_ferrybox_sampledata_2018_tf,
  df_ferrybox_sampledata_2019_tf)

# names(df_ferrybox_sampledata_fa)
# names(df_ferrybox_sampledata_tf)

# df_ferrybox_sampledata <- 
#   bind_rows()
```

### d. Save sample files

``` r
saveRDS(df_ferrybox_sampledata_fa,
        "Datasett/21_df_ferrybox_sampledata_fa.rds")
saveRDS(df_ferrybox_sampledata_tf,
        "Datasett/21_df_ferrybox_sampledata_tf.rds")

openxlsx::write.xlsx(df_ferrybox_sampledata_fa,
                     "Datasett/Ferrybox_samples_OneDrive/FA_2018-19_automatic_samples.xlsx")
```

    ## Note: zip::zip() is deprecated, please use zip::zipr() instead

``` r
openxlsx::write.xlsx(df_ferrybox_sampledata_tf,
                     "Datasett/Ferrybox_samples_OneDrive/TF_2018-19_automatic_samples.xlsx")
```

### e. Sample numbers used in files

Fantasy:  
\- Mostly MANUAL\_SAMPLE\_COUNTER = 0 and AUTOMATIC\_SAMPLE\_COUNTER =
1-24  
\- For a few lines MANUAL\_SAMPLE\_COUNTER = 1-24 and
AUTOMATIC\_SAMPLE\_COUNTER = 0  
Trollfjord:  
\- Same but variables are called SAMPLE\_MAN\_NUM and SAMPLE\_AUTO\_NUM

``` r
cat("================================================\n")
cat("     FANTASY\n")
#
# Fantasy
#
cat("----------------------------\n")
cat("MANUAL_SAMPLE_COUNTER == 0\n")
df_ferrybox_sampledata_fa %>% 
  filter(MANUAL_SAMPLE_COUNTER == 0) %>% 
  xtabs(~AUTOMATIC_SAMPLE_COUNTER, .)

cat("----------------------------\n")
cat("AUTOMATIC_SAMPLE_COUNTER == 0\n")
df_ferrybox_sampledata_fa %>% 
  filter(AUTOMATIC_SAMPLE_COUNTER == 0) %>% 
  xtabs(~MANUAL_SAMPLE_COUNTER, .)

cat("----------------------------\n")
cat("Both == 0\n")
df_ferrybox_sampledata_fa %>% 
  filter(MANUAL_SAMPLE_COUNTER == 0 & AUTOMATIC_SAMPLE_COUNTER == 0) %>% nrow()

cat("================================================\n")
cat("     TROLLFJORD\n")
#
# Trollfjord
#

cat("----------------------------\n")
cat("SAMPLE_MAN_NUM == 0\n")
df_ferrybox_sampledata_tf %>% 
  filter(SAMPLE_MAN_NUM == 0) %>%
  xtabs(~SAMPLE_AUTO_NUM, .)

cat("----------------------------\n")
cat("SAMPLE_AUTO_NUM == 0\n")
df_ferrybox_sampledata_tf %>% 
  filter(SAMPLE_AUTO_NUM == 0) %>%
  xtabs(~SAMPLE_MAN_NUM, .)
```

    ## ================================================
    ##      FANTASY
    ## ----------------------------
    ## MANUAL_SAMPLE_COUNTER == 0
    ## AUTOMATIC_SAMPLE_COUNTER
    ##  0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 
    ## 53 44 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 45 
    ## ----------------------------
    ## AUTOMATIC_SAMPLE_COUNTER == 0
    ## MANUAL_SAMPLE_COUNTER
    ##  0  1  2  3  4  5  6  7 10 11 13 14 15 16 17 19 20 22 23 24 
    ## 53  6  6  4  3  4  3  1  1  1  1  1  1  1  1  1  1  1  1  1 
    ## ----------------------------
    ## Both == 0
    ## [1] 53
    ## ================================================
    ##      TROLLFJORD
    ## ----------------------------
    ## SAMPLE_MAN_NUM == 0
    ## SAMPLE_AUTO_NUM
    ##   0   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24 
    ## 184  24  25  25  23  25  24  27  50  23  26  26  26  26  26  25  47  25  28  27  26  28  37  28  56 
    ## ----------------------------
    ## SAMPLE_AUTO_NUM == 0
    ## SAMPLE_MAN_NUM
    ##   0   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24  25  26  28  31 
    ## 184   1   5   4   4   2   1   2   2   1   2   3   1   3   2   1   1   1   1   1   1   1   2   1   1   1   2   1   1

## 4\. Match automatic samples

Find which sample numbers (in Ferrybox files) belongs to which stations

### Set time and direction for Trollfjord

``` r
df_ferrybox_sampledata_tf <- df_ferrybox_sampledata_tf %>%
  mutate(
    x1 = as.character(SYSTEM_DATE),
    x2 = as.character(SYSTEM_TIME) %>% substr(12,19),
    Date = ymd(x1),
    Time = ymd_hms(paste(x1, x2))) %>%   # select(x1, x2, Date, Time)
  select(-x1, -x2, -SYSTEM_TIME, -SYSTEM_DATE) %>%
  arrange(Time) %>%
  mutate(
    dhour = (as.numeric(Time) - lag(as.numeric(Time), order_by = Time))/3600,
    dlat = GPS_LAT - lag(GPS_LAT, order_by = Time),
    dlon = GPS_LON - lag(GPS_LON, order_by = Time),
    ) %>%  #  elect(Date, Time, GPS_LAT, dhour, dlat)
  select(SHIP_CODE, Date, Time, dhour, dlat, dlon, SAMPLE_AUTO_NUM, SAMPLE_MAN_NUM, TRIP, everything())
```

### Prepare ferrybox samples data for mapping

``` r
#
# Fantasy (sf_points_fa)
#

# Set rownames to "FullStationName" - these will show up when you hover over points with the mouse
df_points <- df_ferrybox_sampledata_fa %>%
  filter(AUTOMATIC_SAMPLE_COUNTER > 0) %>%
  # "copy" coordinates so the show up in popup
  mutate(Lon = GPS_LONGITUDE, Lat = GPS_LATITUDE) %>%  
  as.data.frame()   # read_excel makes a tibble, but you can't set row names for tibbles 

# Set rownames (shown at mouse hover) - must be unique
rownames(df_points) <- with(df_points,
                            paste(SHIP_CODE, AUTOMATIC_SAMPLE_COUNTER, 
                                  SYSTEM_DATE_DMY))

sf_points_fa <- st_as_sf(df_points,
                          coords = c("GPS_LONGITUDE", "GPS_LATITUDE"),
                          crs = "+proj=longlat +ellps=WGS84")

#
# Trollfjord (sf_points_tf)
#

# Set rownames to "FullStationName" - these will show up when you hover over points with the mouse
df_points <- df_ferrybox_sampledata_tf %>%
  filter(SAMPLE_AUTO_NUM > 0) %>%
  # "copy" coordinates so the show up in popup
  mutate(Lon = GPS_LON, Lat = GPS_LAT) %>%  
  as.data.frame()   # read_excel makes a tibble, but you can't set row names for tibbles 

# Set rownames (shown at mouse hover) - must be unique
rownames(df_points) <- with(df_points,
                            paste(SHIP_CODE, SAMPLE_AUTO_NUM, 
                                  Time))

sf_points_tf <- st_as_sf(df_points,
                          coords = c("GPS_LON", "GPS_LAT"),
                          crs = "+proj=longlat +ellps=WGS84")
```

### Check Fantasy samples in map

``` r
m_fa <- mapview(sf_points_fa, alpha.regions = 0.2)
m_fa
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-74-1.png)<!-- -->

### Check Trollfjord samples in map

``` r
m_tf <- mapview(sf_points_tf, alpha.regions = 0.2)
m_tf
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-75-1.png)<!-- -->

### Check stations in map

``` r
df_aqm_stations <- as.data.frame(df_aqm_stations)

rownames(df_aqm_stations) <- df_aqm_stations$StationCode  

sf_points_stations <- st_as_sf(df_aqm_stations,
                       coords = c("Longitude", "Latitude"),
                       crs = "+proj=longlat +ellps=WGS84")

mapview(sf_points_stations)
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-76-1.png)<!-- -->

### Stations vs Fantasy sample positions

Correspondence:  
\- VT4 = sample no 21-22

``` r
m_fa +mapview(sf_points_stations, color = "red") 
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-77-1.png)<!-- -->

### Stations vs Trollfjord sample positions

Correspondence:  
\- VT4 = sample no 23-24  
\- VT72 = sample no 21-22  
\- VT23 = sample no 17-18  
\- VT80 = sample no 11-12  
\- VT45 = sample no 13-14  
\- VT22 = sample no 15-16  
\- VR25 = sample no 6-7  
\- VR23 = sample no 4-5  
\- VR76 = sample no 2-3

``` r
m_tf + mapview(sf_points_stations, color = "red") 
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-78-1.png)<!-- -->

### Check specific Trollfjord sample number

  - In contrast to Fantasy, there is a bit mess in Trollfjord’s
    SAMPLE\_AUTO\_NUM  
  - They are all over

<!-- end list -->

``` r
# Trollfjord
mapview(sf_points_tf %>% filter(SAMPLE_AUTO_NUM == 13), alpha.regions = 0.2)
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-79-1.png)<!-- -->

``` r
#
# Check Fantasy in contrast: sample 21 and 22 are always at the right place
#
if (FALSE){
  mapview(sf_points_fa %>% filter(AUTOMATIC_SAMPLE_COUNTER == 21), alpha.regions = 0.2)
  mapview(sf_points_fa %>% filter(AUTOMATIC_SAMPLE_COUNTER == 22), alpha.regions = 0.2)
}
```

### Trollfjord: Get Ferrybox samples in the vicinity of specific lon, lat

Only for info, not used later  
\- For each station, shows SAMPLE\_AUTO\_NUM with number of data points
in paranthesis - Shows that we have a problem  
\- For some stations, all the closest SAMPLE\_AUTO\_NUM are two numbers
(e.g. 13 and 14 for VT45)

``` r
#
# Pick trollfjord data from a given box around lon, lat
#
# tol_lon, tol_lat = tolerance in lon,lat direction
#
get_trollfjord_from_box <- function(lon, lat, tol_lon, tol_lat){
  df_ferrybox_sampledata_tf %>%
    filter(
      GPS_LON >= (lon - tol_lon) &
      GPS_LON <= (lon + tol_lon) &
        GPS_LAT >= (lat - tol_lat) &
        GPS_LAT <= (lat + tol_lat))
}

# df_aqm_stations %>%  filter(StationCode == "VT72")
# Test
# get_trollfjord_from_box(5.5877, 62.3066, 0.04, 0.04) %>% xtabs(~SAMPLE_AUTO_NUM, .)

# Test
# df <- with(df_aqm_stations %>%  filter(StationCode == "VT12"),
#            get_trollfjord_from_box(Longitude, Latitude, 0.04, 0.04))

#
# 
summarize_trollfjord_from_box <- function(...){
  df <- get_trollfjord_from_box(...) %>%
    filter(SAMPLE_AUTO_NUM > 0)
  val <- xtabs(~SAMPLE_AUTO_NUM, df)
  stat <- xtabs(~SAMPLE_AUTO_NUM, df) %>% names()
  if (length(val > 0)){
    result <- paste(stat, paste0("(", val, ")")) %>% paste(collapse = ", ")
  } else {
    result <- NA
  }
  result
}
# Test
# summarize_trollfjord_from_box(5.5877, 62.3066, 0.04, 0.04)

#
# Add variable "TF_stations"
#
df_aqm_stations$TF_stations <- df_aqm_stations %>%
  select(Longitude, Latitude) %>%
  as.list() %>%
  pmap_chr(~summarize_trollfjord_from_box(.x, .y, 0.08, 0.04))

df_aqm_stations %>%
  select(StationCode, TF_stations)
```

    ##      StationCode                  TF_stations
    ## VT4          VT4                         <NA>
    ## VT76        VT76       2 (11), 3 (22), 4 (11)
    ## VR23        VR23       4 (11), 5 (23), 6 (12)
    ## VR25        VR25       6 (11), 7 (23), 8 (11)
    ## VT80        VT80 6 (1), 7 (1), 11 (7), 12 (7)
    ## VT45        VT45             13 (21), 14 (21)
    ## VT22        VT22     15 (20), 16 (22), 24 (1)
    ## VT23        VT23      5 (1), 17 (20), 18 (20)
    ## VT72        VT72     21 (21), 22 (21), 24 (2)
    ## VT12        VT12             23 (20), 24 (20)

### Check VT80 + VT23 as example

Only for info, not used later

``` r
df <- with(df_aqm_stations %>%  filter(StationCode == "VT80"),
           get_trollfjord_from_box(Longitude, Latitude, 0.04, 0.04))

# df %>% arrange(Time)

df <- with(df_aqm_stations %>%  filter(StationCode == "VR23"),
           get_trollfjord_from_box(Longitude, Latitude, 0.04, 0.04))

# df %>% arrange(Time)
```

## 5\. Trollfjord: Get closest station for each ferrybox data point

### Make UTM coordinates

``` r
library(sp)
crs_longlat <- "+proj=longlat +ellps=WGS84 +datum=WGS84"
crs_utm <- "+proj=utm +zone=32 +ellps=WGS84 +datum=WGS84 +units=m"

#
# a. Add UTM coor to 'df_ferrybox_sampledata_tf'
#
SP <- SpatialPoints(df_ferrybox_sampledata_tf[,c("GPS_LON", "GPS_LAT")],
         proj4string=CRS(crs_longlat)
         )
SP.UTM <- spTransform(SP, CRS(crs_utm))
# Add transformed coords to data set
df_ferrybox_sampledata_tf$UTM_x <- SP.UTM@coords[,1]
df_ferrybox_sampledata_tf$UTM_y <- SP.UTM@coords[,2]


#
# b. Add UTM coor to 'df_aqm_stations'
#
SP <- SpatialPoints(df_aqm_stations[,c("Longitude", "Latitude")],
         proj4string=CRS(crs_longlat)
         )
SP.UTM <- spTransform(SP, CRS(crs_utm))
# Add transformed coords to data set
df_aqm_stations$UTM_x <- SP.UTM@coords[,1]
df_aqm_stations$UTM_y <- SP.UTM@coords[,2]

plot(UTM_y ~ UTM_x, df_ferrybox_sampledata_tf)
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-82-1.png)<!-- -->

``` r
plot(UTM_y ~ UTM_x, df_aqm_stations)
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-82-2.png)<!-- -->

### Function

``` r
#
# Returns distance to give station (in km)
#
get_distance <- function(station, ferryboxdata){
  df_station <- df_aqm_stations %>% filter(StationCode %in% station)
  result <- ferryboxdata %>%
    mutate(dx = (UTM_x - df_station$UTM_x[1])/1000,
           dy = (UTM_y - df_station$UTM_y[1])/1000,
           Dist = sqrt((dx^2) + (dy^2))
    ) %>%
    select(Dist)
  names(result) <- station
  result
}
# test
# x <- get_distance("VT76", df_ferrybox_sampledata_tf)
# range(x, na.rm = TRUE)
```

### Make distance matrix and find closest station

``` r
stations <- df_aqm_stations %>%
  filter(StationCode != "VT4") %>%    # Skagerrak
  pull(StationCode)

distance_matrix <- stations %>% map_dfc(get_distance, ferryboxdata = df_ferrybox_sampledata_tf)

# For test:
# distance_matrix <- distance_matrix[1:4,]
# distance_matrix

distance_minimum <- data.frame(
  Distance_closest = apply(distance_matrix, 1, min),
  Stat_no = apply(distance_matrix, 1, which.min)
)
distance_minimum$StationCode_closest <- stations[distance_minimum$Stat_no]

head(distance_minimum)
```

    ##   Distance_closest Stat_no StationCode_closest
    ## 1         191.2116       3                VR25
    ## 2         191.2296       3                VR25
    ## 3         191.2260       3                VR25
    ## 4         191.2322       3                VR25
    ## 5         191.2286       3                VR25
    ## 6         191.2286       3                VR25

``` r
if (!"StationCode_closest" %in% names(df_ferrybox_sampledata_tf)){
  df_ferrybox_sampledata_tf <- bind_cols(
    df_ferrybox_sampledata_tf,
    distance_minimum %>% select(StationCode_closest, Distance_closest)
    )
}

#
# This was overkill:
#
# # Extra functions for second lowest number and index of second lowest number
# min.second <- function(x) sort(x)[2]
# which.min.second <- function(x) which(rank(x) == 2)[1]
# # Test
# # min.second(c(3,6,5,9))
# # which.min.second(c(3,6,5,9))
# 
# distance_minimum <- data.frame(
#   Distance1 = apply(distance_matrix, 1, min),
#   Distance2 = apply(distance_matrix, 1, min.second),
#   Stat_no1 = apply(distance_matrix, 1, which.min),
#   Stat_no2 = apply(distance_matrix, 1, which.min.second)
# )
# distance_minimum$StationCode_min1 <- stations[distance_minimum$Stat_no1]
# distance_minimum$StationCode_min2 <- stations[distance_minimum$Stat_no2]
```

### Save ‘df\_ferrybox\_sampledata\_tf’

``` r
saveRDS(df_ferrybox_sampledata_tf,
        "Datasett/21_df_ferrybox_sampledata_tf_stations.rds")

openxlsx::write.xlsx(df_ferrybox_sampledata_tf,
                     "Datasett/Ferrybox_samples_OneDrive/TF_2018-19_automatic_samples_stations.xlsx")
```

## 6\. Check positions of manual samples

Only for information

### Check samples in map - prepare data

``` r
# Reload the old one
df_ferrybox_sampledata_tf <- readRDS("Datasett/21_df_ferrybox_sampledata_tf.rds")


library(mapview)
library(sf)

#
# Fantasy (sf_points_fa)
#

# Set rownames to "FullStationName" - these will show up when you hover over points with the mouse
df_points <- df_ferrybox_sampledata_fa %>%
  filter(MANUAL_SAMPLE_COUNTER > 0) %>%
  # "copy" coordinates so the show up in popup
  mutate(Lon = GPS_LONGITUDE, Lat = GPS_LATITUDE) %>%  
  as.data.frame()   # read_excel makes a tibble, but you can't set row names for tibbles 

# Set rownames (shown at mouse hover) - must be unique
rownames(df_points) <- with(df_points,
                            paste(SHIP_CODE, MANUAL_SAMPLE_COUNTER, 
                                  SYSTEM_DATE_DMY, SYSTEM_TIME))

sf_points_fa <- st_as_sf(df_points,
                          coords = c("GPS_LONGITUDE", "GPS_LATITUDE"),
                          crs = "+proj=longlat +ellps=WGS84")

#
# Trollfjord (sf_points_tf)
#

# Set rownames to "FullStationName" - these will show up when you hover over points with the mouse
df_points <- df_ferrybox_sampledata_tf %>%
  filter(SAMPLE_MAN_NUM > 0) %>%
  # "copy" coordinates so the show up in popup
  mutate(Lon = GPS_LON, Lat = GPS_LAT) %>%  
  as.data.frame()   # read_excel makes a tibble, but you can't set row names for tibbles 

# Set rownames (shown at mouse hover) - must be unique
rownames(df_points) <- with(df_points,
                            paste(SHIP_CODE, SAMPLE_MAN_NUM, 
                                  SYSTEM_DATE, SYSTEM_TIME))

sf_points_tf <- st_as_sf(df_points,
                          coords = c("GPS_LON", "GPS_LAT"),
                          crs = "+proj=longlat +ellps=WGS84")
```

### Check Fantasy samples in map

``` r
m_fa <- mapview(sf_points_fa, alpha.regions = 0.2)
m_fa
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-87-1.png)<!-- -->

### Check Trollfjord samples in map

``` r
m_fa <- mapview(sf_points_tf, alpha.regions = 0.2)
m_fa
```

![](21_FBstations_files/figure-gfm/unnamed-chunk-88-1.png)<!-- -->


