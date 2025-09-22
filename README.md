# Case Study: How does a bike-share navigate speedy success?

## 1. Introduction
In this case study, I analyzed **Cyclistic (Divvy) bike-share data** to compare the behavior of two types of users:  
- **Casual riders**  
- **Annual members**  

The main goal was to identify usage patterns that could help the marketing team **convert casual riders into members**, driving retention and revenue growth.  

---

## 2. Business Task
- **Key question:** *How do members and casual riders use bikes differently?*  
- **Objective:** Generate actionable insights to support marketing strategies that convert casuals into annual members.  

---

## 3. Data Source
- Datasets: `Divvy_Trips_2019_Q1.csv` and `Divvy_Trips_2020_Q1.csv`.  
- Source: [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com/index.html).  
- Size: 365,000+ trips after cleaning.  

---

## 4. Data Preparation
Main cleaning and transformation steps:  
- Standardized column names (2019 → 2020 schema).  
- Converted `ride_id` and `rideable_type` to character.  
- Created new variables:  
  - `ride_length` (trip duration in seconds).  
  - `day_of_week`, `month`, `year`.  
- Standardized labels in `member_casual` (`Subscriber` → `member`, `Customer` → `casual`).  
- Removed invalid data: negative durations and technical station **HQ QR**.  

**Example code:**
```r
all_trips <- all_trips %>%
  mutate(ride_length = as.numeric(difftime(ended_at, started_at, units = "secs")),
         member_casual = recode(member_casual, "Subscriber" = "member", "Customer" = "casual")) %>%
  filter(start_station_name != "HQ QR", ride_length >= 0)
```

---

## 5. Analysis
### Descriptive Statistics
- **Casuals:** long rides (average 70–110 minutes).  
- **Members:** short and consistent rides (12–15 minutes).  

### By Day of Week
- **Casuals:** heavier usage on weekends.  
- **Members:** consistent usage during weekdays.  

**Example aggregation:**
```r
counts <- all_trips_v2 %>% 
  group_by(member_casual, day_of_week) %>% 
  summarise(avg_ride_length = mean(ride_length, na.rm = TRUE), .groups = "drop")
```

---

## 6. Visualizations  

### Number of rides by day of week
```r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()) %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")
```
Resulting chart:  
![Number of rides](plots/number_of_rides.png)


---

### Average trip duration by day of week
```r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(average_duration = mean(ride_length)) %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")
```
Resulting chart:  
![Average Duration](plots/average_duration.png) 

---

## 7. Key Findings
- **Casual riders**: longer trips, concentrated on weekends.  
- **Members**: shorter and consistent trips, mainly commuting.  
- Marketing opportunity: **convert weekend casuals into members** with flexible plans and seasonal promotions.  

---

## 8. Tools Used
- **R (tidyverse, ggplot2)** → data wrangling, analysis, and visualization.  
- **Excel** → seconds → hh:mm:ss conversion and validation.  
- **Tableau / Power BI** → optional dashboards for presentation.  

---

## 9. Conclusion
This analysis shows that Cyclistic can grow profitability by **converting casual riders into annual members**, tailoring campaigns to their behavior differences:  
- Offers targeted to weekend casuals.  
- Additional perks for frequent weekday riders.  


- `script.R` → your full R code.  
- `plots/` → exported PNG charts.  
- `data/` → small datasets or link to original Divvy data.  
