# Google_Certification_Capstone_Project.

#Case Study: How Does a Bike-Share Navigate Speedy Success?

Author:Peter Yeboah
Date  :07/17/2023

The Ask, Prepare, Process, Analyse, Share, Act (APPASA) data analystic process will be followed to complete the this capstone assignment.

Ask Phase
How do annual members and casual riders use Cyclistic bikes differently?
The goal of the assigned business task is to investigate the bike share data for differences in usage between annual members and casual riders. The insights gained will be shared with the Cyclistic marketing team. The Director of Marketing is interested in expanding annual memberships by designing targeted ad campaigns for converting casual riders to annual members.

Prepare Phase
The data for the project is provided by Motivate International, Inc. under this license. NOTE: The data sets have a different name because Cyclistic is a fictional company created for the purposes of the capstone project as determined by the Google Data Analytics Professional Certificate program.

The data has been organized in monthly and quarterly periods. Since this project was started in April/May of 2022, the data from March 2021 to March 2022 has been selected.

Limitations
The data has been de-personalized to safeguard the privacy of users. In particular, this means it is not possible to connect past purchases to credit card numbers and determine if casual riders live in the service area or purchased multiple single passes.

Process Phase
Data processing and analyzing will primary occur in RStudio using the R programming language with supplement visualizations done via Tableau.

Open R Libraries
Throughout this analysis we’ll make use of the following libraries. If they are not already installed, please do so before opening them.

## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──
## ✓ ggplot2 3.3.6     ✓ purrr   0.3.4
## ✓ tibble  3.1.6     ✓ dplyr   1.0.8
## ✓ tidyr   1.2.0     ✓ stringr 1.4.0
## ✓ readr   2.1.2     ✓ forcats 0.5.1
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
## 
## Attaching package: 'lubridate'
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
Combine 12-Month Data
The data is separated by month; one CSV file per month. In order to order analyze all 12-months worth of data it’s necessary to combine the files as follows. NOTE: The directory path will vary depending on where the original files were downloaded/saved.

# setting up files 
file_names <- dir(path = "Desktop/Code/Courses/GoogleDataAnalytics/8_CaseStudy/Case_Study_01_Cyclistic/Datasets", 
                  pattern = NULL, all.files = FALSE, full.names = FALSE, 
                  recursive = FALSE, ignore.case = FALSE, include.dirs = FALSE, 
                  no.. = FALSE)

# creating df for last 12 months of data
cyclistic_df <- do.call(rbind, lapply(file_names, read.csv))

# export df to CSV file
write.csv(cyclistic_df, "cyclistic_202103-202203.csv", row.names = TRUE) # completed 2022-04-21
Create data frame
Once we’ve combined the necessary data into a single CSV file, we can read it and store in a data frame. This will allow for ease of use during the cleaning process and the analysis process.

bikeshare_df <- read_csv("Datasets/cyclistic_202103-202203.csv")
## New names:
## Rows: 5952028 Columns: 14
## ── Column specification
## ──────────────────────────────────────────────────────── Delimiter: "," chr
## (7): ride_id, rideable_type, start_station_name, start_station_id, end_... dbl
## (5): ...1, start_lat, start_lng, end_lat, end_lng dttm (2): started_at,
## ended_at
## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
## Specify the column types or set `show_col_types = FALSE` to quiet this message.
## • `` -> `...1`
Inspecting data frame
Quick view using head() and summary()

head(bikeshare_df)
## # A tibble: 6 × 14
##    ...1 ride_id          rideable_type started_at          ended_at           
##   <dbl> <chr>            <chr>         <dttm>              <dttm>             
## 1     1 CFA86D4455AA1030 classic_bike  2021-03-16 08:32:30 2021-03-16 08:36:34
## 2     2 30D9DC61227D1AF3 classic_bike  2021-03-28 01:26:28 2021-03-28 01:36:55
## 3     3 846D87A15682A284 classic_bike  2021-03-11 21:17:29 2021-03-11 21:33:53
## 4     4 994D05AA75A168F2 classic_bike  2021-03-11 13:26:42 2021-03-11 13:55:41
## 5     5 DF7464FBE92D8308 classic_bike  2021-03-21 09:09:37 2021-03-21 09:27:33
## 6     6 CEBA8516FD17F8D8 classic_bike  2021-03-20 11:08:47 2021-03-20 11:29:39
## # … with 9 more variables: start_station_name <chr>, start_station_id <chr>,
## #   end_station_name <chr>, end_station_id <chr>, start_lat <dbl>,
## #   start_lng <dbl>, end_lat <dbl>, end_lng <dbl>, member_casual <chr>
summary(bikeshare_df)
##       ...1           ride_id          rideable_type     
##  Min.   :      1   Length:5952028     Length:5952028    
##  1st Qu.:1488008   Class :character   Class :character  
##  Median :2976014   Mode  :character   Mode  :character  
##  Mean   :2976014                                        
##  3rd Qu.:4464021                                        
##  Max.   :5952028                                        
##                                                         
##    started_at                     ended_at                   start_station_name
##  Min.   :2021-03-01 00:01:09   Min.   :2021-03-01 00:06:28   Length:5952028    
##  1st Qu.:2021-06-15 21:08:34   1st Qu.:2021-06-15 21:35:02   Class :character  
##  Median :2021-08-13 20:17:38   Median :2021-08-13 20:38:45   Mode  :character  
##  Mean   :2021-08-20 17:11:57   Mean   :2021-08-20 17:33:33                     
##  3rd Qu.:2021-10-12 08:05:40   3rd Qu.:2021-10-12 08:19:54                     
##  Max.   :2022-03-31 23:59:47   Max.   :2022-04-01 22:10:12                     
##                                                                                
##  start_station_id   end_station_name   end_station_id       start_lat    
##  Length:5952028     Length:5952028     Length:5952028     Min.   :41.64  
##  Class :character   Class :character   Class :character   1st Qu.:41.88  
##  Mode  :character   Mode  :character   Mode  :character   Median :41.90  
##                                                           Mean   :41.90  
##                                                           3rd Qu.:41.93  
##                                                           Max.   :45.64  
##                                                                          
##    start_lng         end_lat         end_lng       member_casual     
##  Min.   :-87.84   Min.   :41.39   Min.   :-88.97   Length:5952028    
##  1st Qu.:-87.66   1st Qu.:41.88   1st Qu.:-87.66   Class :character  
##  Median :-87.64   Median :41.90   Median :-87.64   Mode  :character  
##  Mean   :-87.65   Mean   :41.90   Mean   :-87.65                     
##  3rd Qu.:-87.63   3rd Qu.:41.93   3rd Qu.:-87.63                     
##  Max.   :-73.80   Max.   :42.17   Max.   :-87.49                     
##                   NA's   :4883    NA's   :4883
Inspect column names:

##  [1] "...1"               "ride_id"            "rideable_type"     
##  [4] "started_at"         "ended_at"           "start_station_name"
##  [7] "start_station_id"   "end_station_name"   "end_station_id"    
## [10] "start_lat"          "start_lng"          "end_lat"           
## [13] "end_lng"            "member_casual"
After inspection, there are is an extra column “… 1” which is not needed since it only contains a list of numbers which correspond to the row number - this was created when the the 12-months worth of data was created. This will need to be removed in the cleaning process.

Check the total number of rows is 5,952,028 after combining the 12-month data:

## [1] 5952028
Cleaning & Formatting the Data
Confirm Correct Categories
Prior to 2020, Cyclistic used different names for casual and member riders. This data is from 2021, but we still want to confirm that the categorical terms for casual riders (“casual”) and annual members (“members”) are still being used.

## 
##  casual  member 
## 2630575 3321453
Add Columns for Date, Month, Year, Day of the Week, Ride Length
In order to analyze ride usage based on the month, day, and year, we need to add columns for each.

bikeshare_df$date <- as.Date(bikeshare_df$started_at)
bikeshare_df$month <- format(as.Date(bikeshare_df$date), "%m")
bikeshare_df$day <- format(as.Date(bikeshare_df$date), "%d")
bikeshare_df$year <- format(as.Date(bikeshare_df$date), "%Y")
bikeshare_df$day_of_week <- format(as.Date(bikeshare_df$date), "%A")
bikeshare_df$ride_length <- difftime(bikeshare_df$ended_at,bikeshare_df$started_at)
Convert Ride Length from Factor to Numeric
is.factor(bikeshare_df$ride_length)
## [1] FALSE
bikeshare_df$ride_length <- as.numeric(as.character(bikeshare_df$ride_length))
is.numeric(bikeshare_df$ride_length)
## [1] TRUE
Remove “bad” data
The data frame includes a few hundred entries when bikes were taken out of docks and checked for quality by Divvy or ride_length was negative. We will create a new version of the data frame (v2) since data is being removed.

bikeshare_df_v2 <- bikeshare_df[!(bikeshare_df$ride_length<0),] # removes neg values
bikeshare_df_v2 <- mutate(bikeshare_df_v2, ...1 = NULL) # removes extra col
Inspect new data frame
head(bikeshare_df_v2)
## # A tibble: 6 × 19
##   ride_id rideable_type started_at          ended_at            start_station_n…
##   <chr>   <chr>         <dttm>              <dttm>              <chr>           
## 1 CFA86D… classic_bike  2021-03-16 08:32:30 2021-03-16 08:36:34 Humboldt Blvd &…
## 2 30D9DC… classic_bike  2021-03-28 01:26:28 2021-03-28 01:36:55 Humboldt Blvd &…
## 3 846D87… classic_bike  2021-03-11 21:17:29 2021-03-11 21:33:53 Shields Ave & 2…
## 4 994D05… classic_bike  2021-03-11 13:26:42 2021-03-11 13:55:41 Winthrop Ave & …
## 5 DF7464… classic_bike  2021-03-21 09:09:37 2021-03-21 09:27:33 Glenwood Ave & …
## 6 CEBA85… classic_bike  2021-03-20 11:08:47 2021-03-20 11:29:39 Glenwood Ave & …
## # … with 14 more variables: start_station_id <chr>, end_station_name <chr>,
## #   end_station_id <chr>, start_lat <dbl>, start_lng <dbl>, end_lat <dbl>,
## #   end_lng <dbl>, member_casual <chr>, date <date>, month <chr>, day <chr>,
## #   year <chr>, day_of_week <chr>, ride_length <dbl>
colnames(bikeshare_df_v2)
##  [1] "ride_id"            "rideable_type"      "started_at"        
##  [4] "ended_at"           "start_station_name" "start_station_id"  
##  [7] "end_station_name"   "end_station_id"     "start_lat"         
## [10] "start_lng"          "end_lat"            "end_lng"           
## [13] "member_casual"      "date"               "month"             
## [16] "day"                "year"               "day_of_week"       
## [19] "ride_length"
View(bikeshare_df_v2)
nrow(bikeshare_df_v2) # check num of rows
## [1] 5951881
any(bikeshare_df_v2$start_station_name == "HQ QR") # doesn't exist in data set
## [1] NA
any(bikeshare_df_v2$ride_length < 0) # checking for negative values
## [1] FALSE
Prepare data frame for export
Export clean data
After cleaning, a CSV file will be exported to preserve the clean data and another file will be created for use in Tableau.

write_csv(bikeshare_df_v2, "2022-04-26_cyclistic_clean_data.csv")
Prepare data frame for export to Tableau
Since the data frame containing the clean data is too large to upload to Tableau Public (file limit of 1 GB) a subset of the data frame will be created and exported.

# selection of desired columns to keep for export
myvars <- c("ride_id", "rideable_type", "member_casual", "date", "month", 
            "day", "year", "day_of_week", "ride_length", "start_station_name", 
            "end_station_name")

# store selected columns in a data frame
bikeshare_subset <- bikeshare_df_v2[myvars]

# write subset data frame to CSV file
write_csv(bikeshare_subset, "2022-04-30_cyclistic_subset.csv")
Analyze/Share Phase
Descriptive Analysis
Summary Statistics of ride length (in seconds) for both casual and member riders:

summary(bikeshare_df_v2$ride_length)
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0     395     704    1296    1283 3356649
Comparison between members and casual riders
Comparison of the mean, median, max, and min.

aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = mean)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                   1916.7300
## 2                        member                    803.7535
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = median)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                         950
## 2                        member                         564
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = max)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                     3356649
## 2                        member                       93596
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = min)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                           0
## 2                        member                           0
Observations

We can see that for both the mean and median, casual riders have trips of longer duration than member riders.

Average duration per rider type sorted by day of the week
# first order by day of the week
bikeshare_df_v2$day_of_week <- ordered(bikeshare_df_v2$day_of_week, 
                                       levels=c("Sunday", "Monday", "Tuesday", 
                                                "Wednesday", "Thursday", "Friday", 
                                                "Saturday")) 

# calculate average
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual + 
            bikeshare_df_v2$day_of_week, FUN = mean)
##    bikeshare_df_v2$member_casual bikeshare_df_v2$day_of_week
## 1                         casual                      Sunday
## 2                         member                      Sunday
## 3                         casual                      Monday
## 4                         member                      Monday
## 5                         casual                     Tuesday
## 6                         member                     Tuesday
## 7                         casual                   Wednesday
## 8                         member                   Wednesday
## 9                         casual                    Thursday
## 10                        member                    Thursday
## 11                        casual                      Friday
## 12                        member                      Friday
## 13                        casual                    Saturday
## 14                        member                    Saturday
##    bikeshare_df_v2$ride_length
## 1                    2252.6486
## 2                     922.8562
## 3                    1918.8017
## 4                     781.5540
## 5                    1664.8835
## 6                     753.9494
## 7                    1667.7131
## 8                     756.2363
## 9                    1675.5797
## 10                    753.2709
## 11                   1805.0783
## 12                    787.4889
## 13                   2075.4493
## 14                    902.2964
Average duration sorted by rider type, then day of the week
bikeshare_df_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  #creates weekday field using wday()
  group_by(member_casual, weekday) %>%  #groups by user type and weekday
  summarise(number_of_rides = n()                           #calculates the number of rides and average duration 
            ,average_duration = mean(ride_length)) %>%      # calculates the average duration
  arrange(member_casual, weekday)                               # sorts
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.
## # A tibble: 14 × 4
## # Groups:   member_casual [2]
##    member_casual weekday number_of_rides average_duration
##    <chr>         <ord>             <int>            <dbl>
##  1 casual        Sun              500143            2253.
##  2 casual        Mon              304983            1919.
##  3 casual        Tue              286834            1665.
##  4 casual        Wed              295283            1668.
##  5 casual        Thu              299120            1676.
##  6 casual        Fri              372054            1805.
##  7 casual        Sat              572097            2075.
##  8 member        Sun              406002             923.
##  9 member        Mon              462262             782.
## 10 member        Tue              513632             754.
## 11 member        Wed              522687             756.
## 12 member        Thu              491308             753.
## 13 member        Fri              470988             787.
## 14 member        Sat              454488             902.
Observations

On average, - casual riders ride longer than member riders - member riders take more rides during the work week (M-F) than casual riders - Casual riders take more rides during the weekend

Visualizations
Plot: Number of Rides by Rider Type
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


Observations

Casual riders take more rides on the weekend than member riders.

Plot: Average Ride Length per Day by Rider Type
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


Observations

On average, casual riders take rides of longer duration than member riders.

Tableau Plots
Duration per Month

average ride length per month for casual and member riders

Observations

Throughout the year, casual riders have longer average ride lengths than member riders. However, ride length of casual riders peaks during the months of April, May, and June.

Top 10 Start Stations for Casual Riders

top 10 start stations for casual riders

Top 10 End Stations for Casual Riders

top 10 end stations for casual riders

Observations

With few exceptions, the top 10 start and end stations for casual riders are nearly identical. In particular, the top 3 stations (listed below) are the same start and ending points for casual riders. This could be useful for making recommendations for a targeted ad campaign to convert casual riders to member riders.

Top 3 Stations for casual riders: - Streeter Dr & Grand Ave - Millennium Park - Michigan Ave & Oak St

Top 10 Start Stations for Member Riders

top 10 start stations for member riders

Top 10 End Stations for Member Riders

top 10 end stations for member riders

Observations

Similar to the casual riders, the top ten start and end locations for members riders are nearly identical with the exception of Green St & Madison St which appears as the 10th end location for member riders. This could useful for rewarding member riders with special discounts, however that’s outside the realm of the business task.

Act Phase: Key Findings & Recommendations
As reminder, the key limitation for this data was not being able to connect credit information to past purchases (in order to protect user privacy). This prevents us from knowing which casual riders live in the service area and which are travelers or tourists to the area. If the casual riders who live in the service area can be identified through another means that ensures user privacy, this could help the marketing team better target casual riders and convert them to member riders.

Regardless, there are insights that can be gleaned from the above analysis and help guide the actions of the marketing team to achieve the goal of converting casual riders to member riders. In particular, here are my top 3 recommendations:

We know that casual riders take rides of longer duration during the weekends. To encourage casual riders to become members, Cyclistic could partner with special events that occur on weekends. For example, they could partner with concert venues or sporting events.
Casual riders have their peak ride times during the months of April, May, and June. To encourage them to continue using the bike share service, special promotions for membership could be offer outside of peak times in effort to encourage them to continue using the service. This could also be applied during the work-week (M-F) when casual riders are less-likely to use the service.
Lastly, Cyclistic could partner with restaurants and recreational facilities near the Top 10 Start/End locations. As an example a discounted bike share membership could be offered when they purchase an item at a local business near these locations.

Ask Phase
How do annual members and casual riders use Cyclistic bikes differently?
The goal of the assigned business task is to investigate the bike share data for differences in usage between annual members and casual riders. The insights gained will be shared with the Cyclistic marketing team. The Director of Marketing is interested in expanding annual memberships by designing targeted ad campaigns for converting casual riders to annual members.

Prepare Phase
The data for the project is provided by Motivate International, Inc. under this license. NOTE: The data sets have a different name because Cyclistic is a fictional company created for the purposes of the capstone project as determined by the Google Data Analytics Professional Certificate program.

The data has been organized in monthly and quarterly periods. Since this project was started in April/May of 2022, the data from March 2021 to March 2022 has been selected.

Limitations
The data has been de-personalized to safeguard the privacy of users. In particular, this means it is not possible to connect past purchases to credit card numbers and determine if casual riders live in the service area or purchased multiple single passes.

Process Phase
Data processing and analyzing will primary occur in RStudio using the R programming language with supplement visualizations done via Tableau.

Open R Libraries
Throughout this analysis we’ll make use of the following libraries. If they are not already installed, please do so before opening them.

## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──
## ✓ ggplot2 3.3.6     ✓ purrr   0.3.4
## ✓ tibble  3.1.6     ✓ dplyr   1.0.8
## ✓ tidyr   1.2.0     ✓ stringr 1.4.0
## ✓ readr   2.1.2     ✓ forcats 0.5.1
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
## 
## Attaching package: 'lubridate'
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
Combine 12-Month Data
The data is separated by month; one CSV file per month. In order to order analyze all 12-months worth of data it’s necessary to combine the files as follows. NOTE: The directory path will vary depending on where the original files were downloaded/saved.

# setting up files 
file_names <- dir(path = "Desktop/Code/Courses/GoogleDataAnalytics/8_CaseStudy/Case_Study_01_Cyclistic/Datasets", 
                  pattern = NULL, all.files = FALSE, full.names = FALSE, 
                  recursive = FALSE, ignore.case = FALSE, include.dirs = FALSE, 
                  no.. = FALSE)

# creating df for last 12 months of data
cyclistic_df <- do.call(rbind, lapply(file_names, read.csv))

# export df to CSV file
write.csv(cyclistic_df, "cyclistic_202103-202203.csv", row.names = TRUE) # completed 2022-04-21
Create data frame
Once we’ve combined the necessary data into a single CSV file, we can read it and store in a data frame. This will allow for ease of use during the cleaning process and the analysis process.

bikeshare_df <- read_csv("Datasets/cyclistic_202103-202203.csv")
## New names:
## Rows: 5952028 Columns: 14
## ── Column specification
## ──────────────────────────────────────────────────────── Delimiter: "," chr
## (7): ride_id, rideable_type, start_station_name, start_station_id, end_... dbl
## (5): ...1, start_lat, start_lng, end_lat, end_lng dttm (2): started_at,
## ended_at
## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
## Specify the column types or set `show_col_types = FALSE` to quiet this message.
## • `` -> `...1`
Inspecting data frame
Quick view using head() and summary()

head(bikeshare_df)
## # A tibble: 6 × 14
##    ...1 ride_id          rideable_type started_at          ended_at           
##   <dbl> <chr>            <chr>         <dttm>              <dttm>             
## 1     1 CFA86D4455AA1030 classic_bike  2021-03-16 08:32:30 2021-03-16 08:36:34
## 2     2 30D9DC61227D1AF3 classic_bike  2021-03-28 01:26:28 2021-03-28 01:36:55
## 3     3 846D87A15682A284 classic_bike  2021-03-11 21:17:29 2021-03-11 21:33:53
## 4     4 994D05AA75A168F2 classic_bike  2021-03-11 13:26:42 2021-03-11 13:55:41
## 5     5 DF7464FBE92D8308 classic_bike  2021-03-21 09:09:37 2021-03-21 09:27:33
## 6     6 CEBA8516FD17F8D8 classic_bike  2021-03-20 11:08:47 2021-03-20 11:29:39
## # … with 9 more variables: start_station_name <chr>, start_station_id <chr>,
## #   end_station_name <chr>, end_station_id <chr>, start_lat <dbl>,
## #   start_lng <dbl>, end_lat <dbl>, end_lng <dbl>, member_casual <chr>
summary(bikeshare_df)
##       ...1           ride_id          rideable_type     
##  Min.   :      1   Length:5952028     Length:5952028    
##  1st Qu.:1488008   Class :character   Class :character  
##  Median :2976014   Mode  :character   Mode  :character  
##  Mean   :2976014                                        
##  3rd Qu.:4464021                                        
##  Max.   :5952028                                        
##                                                         
##    started_at                     ended_at                   start_station_name
##  Min.   :2021-03-01 00:01:09   Min.   :2021-03-01 00:06:28   Length:5952028    
##  1st Qu.:2021-06-15 21:08:34   1st Qu.:2021-06-15 21:35:02   Class :character  
##  Median :2021-08-13 20:17:38   Median :2021-08-13 20:38:45   Mode  :character  
##  Mean   :2021-08-20 17:11:57   Mean   :2021-08-20 17:33:33                     
##  3rd Qu.:2021-10-12 08:05:40   3rd Qu.:2021-10-12 08:19:54                     
##  Max.   :2022-03-31 23:59:47   Max.   :2022-04-01 22:10:12                     
##                                                                                
##  start_station_id   end_station_name   end_station_id       start_lat    
##  Length:5952028     Length:5952028     Length:5952028     Min.   :41.64  
##  Class :character   Class :character   Class :character   1st Qu.:41.88  
##  Mode  :character   Mode  :character   Mode  :character   Median :41.90  
##                                                           Mean   :41.90  
##                                                           3rd Qu.:41.93  
##                                                           Max.   :45.64  
##                                                                          
##    start_lng         end_lat         end_lng       member_casual     
##  Min.   :-87.84   Min.   :41.39   Min.   :-88.97   Length:5952028    
##  1st Qu.:-87.66   1st Qu.:41.88   1st Qu.:-87.66   Class :character  
##  Median :-87.64   Median :41.90   Median :-87.64   Mode  :character  
##  Mean   :-87.65   Mean   :41.90   Mean   :-87.65                     
##  3rd Qu.:-87.63   3rd Qu.:41.93   3rd Qu.:-87.63                     
##  Max.   :-73.80   Max.   :42.17   Max.   :-87.49                     
##                   NA's   :4883    NA's   :4883
Inspect column names:

##  [1] "...1"               "ride_id"            "rideable_type"     
##  [4] "started_at"         "ended_at"           "start_station_name"
##  [7] "start_station_id"   "end_station_name"   "end_station_id"    
## [10] "start_lat"          "start_lng"          "end_lat"           
## [13] "end_lng"            "member_casual"
After inspection, there are is an extra column “… 1” which is not needed since it only contains a list of numbers which correspond to the row number - this was created when the the 12-months worth of data was created. This will need to be removed in the cleaning process.

Check the total number of rows is 5,952,028 after combining the 12-month data:

## [1] 5952028
Cleaning & Formatting the Data
Confirm Correct Categories
Prior to 2020, Cyclistic used different names for casual and member riders. This data is from 2021, but we still want to confirm that the categorical terms for casual riders (“casual”) and annual members (“members”) are still being used.

## 
##  casual  member 
## 2630575 3321453
Add Columns for Date, Month, Year, Day of the Week, Ride Length
In order to analyze ride usage based on the month, day, and year, we need to add columns for each.

bikeshare_df$date <- as.Date(bikeshare_df$started_at)
bikeshare_df$month <- format(as.Date(bikeshare_df$date), "%m")
bikeshare_df$day <- format(as.Date(bikeshare_df$date), "%d")
bikeshare_df$year <- format(as.Date(bikeshare_df$date), "%Y")
bikeshare_df$day_of_week <- format(as.Date(bikeshare_df$date), "%A")
bikeshare_df$ride_length <- difftime(bikeshare_df$ended_at,bikeshare_df$started_at)
Convert Ride Length from Factor to Numeric
is.factor(bikeshare_df$ride_length)
## [1] FALSE
bikeshare_df$ride_length <- as.numeric(as.character(bikeshare_df$ride_length))
is.numeric(bikeshare_df$ride_length)
## [1] TRUE
Remove “bad” data
The data frame includes a few hundred entries when bikes were taken out of docks and checked for quality by Divvy or ride_length was negative. We will create a new version of the data frame (v2) since data is being removed.

bikeshare_df_v2 <- bikeshare_df[!(bikeshare_df$ride_length<0),] # removes neg values
bikeshare_df_v2 <- mutate(bikeshare_df_v2, ...1 = NULL) # removes extra col
Inspect new data frame
head(bikeshare_df_v2)
## # A tibble: 6 × 19
##   ride_id rideable_type started_at          ended_at            start_station_n…
##   <chr>   <chr>         <dttm>              <dttm>              <chr>           
## 1 CFA86D… classic_bike  2021-03-16 08:32:30 2021-03-16 08:36:34 Humboldt Blvd &…
## 2 30D9DC… classic_bike  2021-03-28 01:26:28 2021-03-28 01:36:55 Humboldt Blvd &…
## 3 846D87… classic_bike  2021-03-11 21:17:29 2021-03-11 21:33:53 Shields Ave & 2…
## 4 994D05… classic_bike  2021-03-11 13:26:42 2021-03-11 13:55:41 Winthrop Ave & …
## 5 DF7464… classic_bike  2021-03-21 09:09:37 2021-03-21 09:27:33 Glenwood Ave & …
## 6 CEBA85… classic_bike  2021-03-20 11:08:47 2021-03-20 11:29:39 Glenwood Ave & …
## # … with 14 more variables: start_station_id <chr>, end_station_name <chr>,
## #   end_station_id <chr>, start_lat <dbl>, start_lng <dbl>, end_lat <dbl>,
## #   end_lng <dbl>, member_casual <chr>, date <date>, month <chr>, day <chr>,
## #   year <chr>, day_of_week <chr>, ride_length <dbl>
colnames(bikeshare_df_v2)
##  [1] "ride_id"            "rideable_type"      "started_at"        
##  [4] "ended_at"           "start_station_name" "start_station_id"  
##  [7] "end_station_name"   "end_station_id"     "start_lat"         
## [10] "start_lng"          "end_lat"            "end_lng"           
## [13] "member_casual"      "date"               "month"             
## [16] "day"                "year"               "day_of_week"       
## [19] "ride_length"
View(bikeshare_df_v2)
nrow(bikeshare_df_v2) # check num of rows
## [1] 5951881
any(bikeshare_df_v2$start_station_name == "HQ QR") # doesn't exist in data set
## [1] NA
any(bikeshare_df_v2$ride_length < 0) # checking for negative values
## [1] FALSE
Prepare data frame for export
Export clean data
After cleaning, a CSV file will be exported to preserve the clean data and another file will be created for use in Tableau.

write_csv(bikeshare_df_v2, "2022-04-26_cyclistic_clean_data.csv")
Prepare data frame for export to Tableau
Since the data frame containing the clean data is too large to upload to Tableau Public (file limit of 1 GB) a subset of the data frame will be created and exported.

# selection of desired columns to keep for export
myvars <- c("ride_id", "rideable_type", "member_casual", "date", "month", 
            "day", "year", "day_of_week", "ride_length", "start_station_name", 
            "end_station_name")

# store selected columns in a data frame
bikeshare_subset <- bikeshare_df_v2[myvars]

# write subset data frame to CSV file
write_csv(bikeshare_subset, "2022-04-30_cyclistic_subset.csv")
Analyze/Share Phase
Descriptive Analysis
Summary Statistics of ride length (in seconds) for both casual and member riders:

summary(bikeshare_df_v2$ride_length)
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0     395     704    1296    1283 3356649
Comparison between members and casual riders
Comparison of the mean, median, max, and min.

aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = mean)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                   1916.7300
## 2                        member                    803.7535
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = median)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                         950
## 2                        member                         564
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = max)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                     3356649
## 2                        member                       93596
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual, FUN = min)
##   bikeshare_df_v2$member_casual bikeshare_df_v2$ride_length
## 1                        casual                           0
## 2                        member                           0
Observations

We can see that for both the mean and median, casual riders have trips of longer duration than member riders.

Average duration per rider type sorted by day of the week
# first order by day of the week
bikeshare_df_v2$day_of_week <- ordered(bikeshare_df_v2$day_of_week, 
                                       levels=c("Sunday", "Monday", "Tuesday", 
                                                "Wednesday", "Thursday", "Friday", 
                                                "Saturday")) 

# calculate average
aggregate(bikeshare_df_v2$ride_length ~ bikeshare_df_v2$member_casual + 
            bikeshare_df_v2$day_of_week, FUN = mean)
##    bikeshare_df_v2$member_casual bikeshare_df_v2$day_of_week
## 1                         casual                      Sunday
## 2                         member                      Sunday
## 3                         casual                      Monday
## 4                         member                      Monday
## 5                         casual                     Tuesday
## 6                         member                     Tuesday
## 7                         casual                   Wednesday
## 8                         member                   Wednesday
## 9                         casual                    Thursday
## 10                        member                    Thursday
## 11                        casual                      Friday
## 12                        member                      Friday
## 13                        casual                    Saturday
## 14                        member                    Saturday
##    bikeshare_df_v2$ride_length
## 1                    2252.6486
## 2                     922.8562
## 3                    1918.8017
## 4                     781.5540
## 5                    1664.8835
## 6                     753.9494
## 7                    1667.7131
## 8                     756.2363
## 9                    1675.5797
## 10                    753.2709
## 11                   1805.0783
## 12                    787.4889
## 13                   2075.4493
## 14                    902.2964
Average duration sorted by rider type, then day of the week
bikeshare_df_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  #creates weekday field using wday()
  group_by(member_casual, weekday) %>%  #groups by user type and weekday
  summarise(number_of_rides = n()                           #calculates the number of rides and average duration 
            ,average_duration = mean(ride_length)) %>%      # calculates the average duration
  arrange(member_casual, weekday)                               # sorts
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.
## # A tibble: 14 × 4
## # Groups:   member_casual [2]
##    member_casual weekday number_of_rides average_duration
##    <chr>         <ord>             <int>            <dbl>
##  1 casual        Sun              500143            2253.
##  2 casual        Mon              304983            1919.
##  3 casual        Tue              286834            1665.
##  4 casual        Wed              295283            1668.
##  5 casual        Thu              299120            1676.
##  6 casual        Fri              372054            1805.
##  7 casual        Sat              572097            2075.
##  8 member        Sun              406002             923.
##  9 member        Mon              462262             782.
## 10 member        Tue              513632             754.
## 11 member        Wed              522687             756.
## 12 member        Thu              491308             753.
## 13 member        Fri              470988             787.
## 14 member        Sat              454488             902.
Observations

On average, - casual riders ride longer than member riders - member riders take more rides during the work week (M-F) than casual riders - Casual riders take more rides during the weekend

Visualizations
Plot: Number of Rides by Rider Type
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


Observations

Casual riders take more rides on the weekend than member riders.

Plot: Average Ride Length per Day by Rider Type
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


Observations

On average, casual riders take rides of longer duration than member riders.

Tableau Plots
Duration per Month

average ride length per month for casual and member riders

Observations

Throughout the year, casual riders have longer average ride lengths than member riders. However, ride length of casual riders peaks during the months of April, May, and June.

Top 10 Start Stations for Casual Riders

top 10 start stations for casual riders

Top 10 End Stations for Casual Riders

top 10 end stations for casual riders

Observations

With few exceptions, the top 10 start and end stations for casual riders are nearly identical. In particular, the top 3 stations (listed below) are the same start and ending points for casual riders. This could be useful for making recommendations for a targeted ad campaign to convert casual riders to member riders.

Top 3 Stations for casual riders: - Streeter Dr & Grand Ave - Millennium Park - Michigan Ave & Oak St

Top 10 Start Stations for Member Riders

top 10 start stations for member riders

Top 10 End Stations for Member Riders

top 10 end stations for member riders

Observations

Similar to the casual riders, the top ten start and end locations for members riders are nearly identical with the exception of Green St & Madison St which appears as the 10th end location for member riders. This could useful for rewarding member riders with special discounts, however that’s outside the realm of the business task.

Act Phase: Key Findings & Recommendations
As reminder, the key limitation for this data was not being able to connect credit information to past purchases (in order to protect user privacy). This prevents us from knowing which casual riders live in the service area and which are travelers or tourists to the area. If the casual riders who live in the service area can be identified through another means that ensures user privacy, this could help the marketing team better target casual riders and convert them to member riders.

Regardless, there are insights that can be gleaned from the above analysis and help guide the actions of the marketing team to achieve the goal of converting casual riders to member riders. In particular, here are my top 3 recommendations:

We know that casual riders take rides of longer duration during the weekends. To encourage casual riders to become members, Cyclistic could partner with special events that occur on weekends. For example, they could partner with concert venues or sporting events.
Casual riders have their peak ride times during the months of April, May, and June. To encourage them to continue using the bike share service, special promotions for membership could be offer outside of peak times in effort to encourage them to continue using the service. This could also be applied during the work-week (M-F) when casual riders are less-likely to use the service.
Lastly, Cyclistic could partner with restaurants and recreational facilities near the Top 10 Start/End locations. As an example a discounted bike share membership could be offered when they purchase an item at a local business near the

