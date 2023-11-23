---
title: "Google Data Analytics Capstone Project"
excerpt: "This project is a comprehensive data analysis case study of Cyclistic, a bike-share program, where I utilized Python to dissect a year's worth of trip data, aiming to understand the distinct usage patterns between casual riders and annual members. The study involved data cleaning, manipulation, and visualization to inform targeted marketing strategies for converting casual riders into annual members.<br/><img src='/images/DAAnalysis500x300.png'>"
collection: projects
---


Jupyter Notebook: [link](https://nbviewer.org/github/ddugan23/ddugan23.github.io/blob/master/files/analyze.ipynb)

## Cyclistic Bike-Share Analysis Report
1. *Business Task*

The primary objective is to understand how annual members and casual riders use Cyclistic bikes differently. This insight will guide Cyclistic's marketing strategy to convert casual riders into annual members, enhancing revenue and customer engagement.

2. *Data Sources*

The analysis utilized historical trip data from Cyclistic, spanning from June 2021 to May 2022. This data includes twelve CSV files, each representing a month's worth of trip data, encompassing various attributes like ride IDs, rideable type, start and end times, station names and IDs, coordinates, and member status (casual or member).

3. *Data Cleaning and Manipulation*

The initial data consolidation involved combining twelve monthly datasets into a single dataset. The data cleaning process entailed:
- Dropping rows with missing latitude and longitude values to ensure accuracy in location-based analysis.
- Converting the 'started_at' and 'ended_at' columns to datetime format for easier manipulation.
- Calculating the trip duration in minutes for further analysis.

4. *Summary of Analysis*

The analysis focused on three different aspects:
- **Average Trip Duration:** The average trip duration was calculated for each user type, revealing that casual riders have significantly longer trip durations compared to annual members.
- **Usage Patterns by Time and Day:** Analysis of trip frequency by day of the week and hour of the day for each user type highlighted distinct usage patterns between casual riders and members.
- **Station Popularity:** The most frequently used start and end stations were identified for each user type, indicating preferences in station usage.

5. *Visualizations and Key Findings*

- **Trip Duration Differences:** A bar chart depicting average trip durations showcased that casual riders tend to use bikes for longer periods.
- **Weekly and Hourly Usage Patterns:** Bar charts for weekly and hourly usage illustrated variances in trip frequencies, with casual riders showing similar hourly peak usage times but different daily peak usage times.
- **Popular Stations:** A bar chart highlighting the top stations for casual users provided insights into the most popular locations for casual bike usage, with the station at Streeter Dr. & Grand Ave. being the starting station for more than double the number of rides compared to any other station.

6. *Top Three Recommendations*

    1.	Targeted Marketing at Popular Stations: Utilize the data on popular stations for casual riders to deploy targeted marketing campaigns at these locations, especially at Streeter Dr. & Grand Ave. station.
    2.	Tailored Membership Plans: Considering the longer trip durations of casual riders, introduce flexible membership plans that cater to their usage patterns, such as weekend or hourly passes that convert to membership credits.
    3.	Engagement through Digital Media: Leverage digital media to showcase the convenience and benefits of membership, especially during peak usage times identified in the analysis. This could include social media campaigns, targeted ads, and success stories of casual riders turning into members.


