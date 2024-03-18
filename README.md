# Cyclistic-bike-share-data-analysis

## Table of Contents

1. [Project Overview](#project-overview)
2. [Data Sources](#data-sources)
3. [Tools](#tools)
4. [Data Preparation](#data-preparation)
5. [Data Processing](#data-processing)
6. [Data Analysis](#data-analysis)
7. [Results](#results)
8. [Recommendations](#recommendations)
9. [Limitations](#limitations)
10. [References](#references)

### Project Overview 

This data analysis project aims to provide insights into how Cyclistic casual riders and annual members use bikes differently. By analyzing various aspects of the historical bike trip data, I seek to identify trends, make data-driven marketing strategies, and gain a deeper understanding of the company's performance. 

### Data Sources

Bike trip data: The primary datasets used for this analysis are the "Divvy_Trips_2019_Q1.zip" and "Divvy_Trips_2020_Q1.zip" files, containing detailed information about each bike's trip. The data has been made available by Motivate International Inc. under this [licence](https://divvybikes.com/data-license-agreement). This is public data that you can use to explore how different customer types are using Cyclistic bikes. But note that data-privacy issues prohibit you from using riders' personally identifiable information. This means that you will not be able to connect pass purchases to credit card numbers to determine if casual riders live in the Cyclistic service area or if they have purchased multiple single passes.

### Tools

- RStudio [Download here](https://posit.co/download/rstudio-desktop/) - Data cleaning, processing, analyzing, and visualization
- Tableau [Download here](https://www.tableau.com/products/desktop/download) - Data visualization

### Data Preparation

In the initial data preparation phase, I performed the following tasks:
1. Download data and store it appropriately.
2. Identify how its organized.
3. Sort and filter the data.
4. Determine the credibility of the data.
5. Wrangle data and combine into a single file.

### Data Processing

1. Inspect the new table/file that has been created checking its dimensions, data types, and statistical summary.
2. Change the 'member_casual' column values to only member or casual.
3. Add columns that list date, month, day, day of the week, year, and length of ride, for each ride.
4. Inspect the structure of the columns.
5. Convert 'ride_length' column from factor to numeric to run calculations
6. Remove records where 'ride_length' is negative, or either have missing start/end station name.
7. Rename and save the new cleaned dataframe.

### Data Analysis

#CONDUCT DESCRIPTIVE ANALYSIS

#Descriptive analysis on ride_length (all figures in seconds)
mean(all_trips_v2$ride_length) #straight average (total ride length / rides)
median(all_trips_v2$ride_length) #midpoint number in the ascending array of ride lengths
max(all_trips_v2$ride_length) #longest ride
min(all_trips_v2$ride_length) #shortest ride

#You can condense the four lines above to one line using summary() on the specific attribute
summary(all_trips_v2$ride_length)

#Compare members and casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)

#See the average ride time by each day for members vs casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)

#Notice that the days of the week are out of order. Let's fix that.
all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))

#Now, let's run the average ride time by each day for members vs casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)

#analyze ridership data by type and weekday
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  #creates weekday field using wday()
  group_by(member_casual, weekday) %>%  #groups by usertype and weekday
  summarise(number_of_rides = n()							#calculates the number of rides and average duration 
            ,average_duration = mean(ride_length)) %>% 		# calculates the average duration
  arrange(member_casual, weekday)								# sorts

#Let's visualize the number of rides by rider type
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")

#Let's create a visualization for average duration
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")

#Create a csv file that we will visualize in Excel, Tableau, or my presentation software
#N.B.: This file location is for a Mac. If you are working on a PC, change the file location accordingly (most likely "C:\Users\YOUR_USERNAME\Desktop\...") to export the data. You can read more here: https://datatofish.com/export-dataframe-to-csv-in-r/
counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
write.csv(counts, file = 'avg_ride_length.csv')

### Results

The analysis results are summarized as follows:
1. Cyclistic has more members than casual riders as customers.
2. Cyclistic's bike trips increased between 2019 and 2020 for both casual and member riders.
3. Cyclistic's casual riders take longer trips than members.
4. Cyclistc's members take more trips than casual riders.
5. Cyclistic's casual riders use electric the most and docked bikes type the least, on the other hand, members use classic the most and electric the least and never docked bike type.

### Recommendations

Based on the analysis, I recommend the following marketing strategies:
1. Cyclistic's number of casual riders is significantly small, therefore, it is best to simply come up with a strategy to get new customers and sign them up as members.
  - One strategy is to terminate single/full day pass tickets and only sell membership tickets.
  - Introduce Cyclistic bikes in other areas.
  - Offer free single day trips to members on weekends or monthly.
2. Since the number of rides increased between 2019 and 2020, Cyclistic should increase number of bikes in highly active areas.
3. One strategy to convert casual riders into members is to offer membership tickets at a cheaper price at end stations if their last trip took a minimum of 60 minutes as we know they take longer trips.
4. Run promotion of membership tickets on weekends as casual riders ride on weekends.
5. Cyclistic should increase the number of electric and classic bikes type as they have more preference than docked bikes.

### Limitations

I had to remove records without start/end station name and those with negative length of ride. Furthermore, removed latitude, longitude, gender, and birthyear columns as 2020 dataset did not have these attributes. I could not analyze rideable type because it was not in a correct format but referenced Samuel Ngai's visualization for this analysis as most vizualizations were matching his analysis.

### References

1. Divvy Exercise R script provided by Google Data Analytics certificate course.
2. [Stack Overflow](https://stackoverflow.com/)
3. [Cyclistic project by Samuel Ngai](https://www.linkedin.com/pulse/google-data-analytics-capstone-project-cyclistic-bike-samuel-ngai/)
4. [YouTube video about how to document a project on GitHub](https://www.youtube.com/watch?v=0N9xekdKCwk)
