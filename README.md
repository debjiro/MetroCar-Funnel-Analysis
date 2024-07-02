# MetroCar-Funnel-Analysis
## Table of Contents
 1.  [About Metrocar](#about-metrocar )
 2.  [Analysis Goal:](#analysis-goal)
 3.  [Tools:](#tools)
 4.  [Data:](#data)
 5.  [Data Analysis:](#data)
 6.  [Overall Conversion Rates:](#overall-conversion-rates)
 7.  [Conversion Rates Across Platforms:](#conversion-rates-across-platforms)
 8.  [Conversion Rates Across Age Range:](#conversion-rates-across-age-range)
 9.  [Revenue Generated Across Platforms and Age range:](#)
 10. [](#)
 11. [](#)
 12. [](#)
 13. [](#)
![](metrocar.png)
## About Metrocar:
Metro car operates within the ride-sharing industry by connecting passengers with drivers through its mobile app
Using the customer funnel analysis we would explore the journey of all our customers(users) who downloaded the app in 2021.
## Analysis Goal:
Understand the customer funnel and identify areas for improvement and optimization
## Tools:
- SQL Server - Data Analysis
- Tableau - For visualisation and creating a story  
## Data: 
App downloads(downloads)  from the year 2021(23.6k)and download stream signups + rides through April  2022
![](customer_funnel.png)
## Data Analysis
```sql

```
## Overall Conversion Rates:
- A significant amount (74.6%) of app downloads result in signups
- Similarly, 70.4 % of signed-up users end up requesting a ride
- Overall, 52% of app downloads result in ride_request
![](overall_conversion_rates.png)
## Conversion Rates Across Platforms:
- Relatively similar conversion rates for the platforms across the funnel
- However, the IOS platform has the highest volume of users based on the wide  funnel chart area
![](connversion_rates_across_platforms.png)
## Conversion Rates Across Age Range:
- Excluding the  Null and Unknown values, the 35-44 age group accounts for the majority of users while the 45-54 makes up the least.
- Also conversion rates are relatively similar throughout the funnel for all age ranges
![](conversion_rates_agerange.png)
## Revenue Generated Across Platforms and Age range:
- The  known age range  of users within 35-44 years using the IOS platform generates the highest revenue
- The least revenue is generated by the 45-54 age range of web users
![](revenue.png)
## Overall Drop-off Rates:
- About half of the users from driver-accept did not proceed to completed rides(-49.23%) drop-off
![](drop_off_rates.png)
## Hourly Ride Request 
- Ride requests throughout the day depict 1st peak period between 8 am and 10 am  and the next peak period in the evening(16:00 - 20:00)hrs
 ![](hourly_ride_request.png) 
## Rating segmentation
- 50.56% rated their experience with the metro car app as above average(4-5)while 39.2% rated the app as either 1 or 2
![](rating.png)
## We can interact with the dashboard [here](https://public.tableau.com/views/MetroCarDashboard/MetroCarDashboard?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)
## We can access the Tableau Story[here](https://public.tableau.com/views/TableauStory-FunnelAnalysisMetroCar/TableauStory-FunnelAnalysisatMetroCar?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link) 
## Key Takeaways
- 1. Although there is a healthy conversion rate across platforms there is room for improvement which could be achieved by offering incentives to users and drivers based on performance.
- 2. There's a notable drop-off between ride requests and driver acceptance, suggesting a need to recruit more trained drivers.
- 3. Our target demographics for the advert should be the 35-44 age group because they represent the working class and would commute often within the peak periods.
- 4. Implementing price surges within observed peak periods in the morning and evening would be an avenue to optimize profits.

## Future Analysis Ideas
- Predominantly used vehicle type in metro car and location of use
- Driver's age group and gender
- Which mobile network connects the most passengers to metro drivers
- Which advertising strategy is most effective in terms of accessing our target demographics
