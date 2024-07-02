# MetroCar-Funnel-Analysis
## Table of Contents
 1.  [About Metrocar:](#about-metrocar )
 2.  [Analysis Goal:](#analysis-goal)
 3.  [Tools:](#tools)
 4.  [Data:](#data)
 5.  [Data Analysis:](#data)
 6.  [Overall Conversion Rates:](#overall-conversion-rates)
 7.  [Conversion Rates Across Platforms:](#conversion-rates-across-platforms)
 8.  [Conversion Rates Across Age Range:](#conversion-rates-across-age-range)
 9.  [Revenue Generated Across Platforms and Age range:](#revenue-generated-across-platforms-and-age-range)
 10. [Overall Drop-off Rates:](#overall-drop-off-rates)
 11. [Hourly Ride Request:](#hourly-ride-request)
 12. [Rating Segments:](#rating-segments)
 13. [Recommendations:](#recommendations)
 14. [Future Analysis Ideas:](#future-analysis-ideas)

     
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
WITH downloads AS ( 

  SELECT

    ad.app_download_key, 

    ad.platform, 

    ad.download_ts :: DATE 

  FROM app_downloads ad 

  GROUP BY ad.app_download_key, ad.platform, ad.download_ts 

), 

signedup_users AS ( 

  SELECT 

  s.user_id, 

  s.age_range, 

  s.session_id 

  FROM signups s 

  GROUP BY s.user_id, s.age_range, s.session_id  

), 

user_ride_requested AS ( 

  SELECT  

  	COUNT(DISTINCT rr.ride_id) num_req, 

  	s.user_id 

  FROM ride_requests rr 

  JOIN signups s ON rr.user_id = s.user_id 

WHERE rr.request_ts IS NOT NULL 

  GROUP BY  s.user_id 

),  

driver_accepted AS ( 

  SELECT 

    COUNT(DISTINCT rr.ride_id) num_ac, 

  	s.user_id 

  FROM ride_requests rr 

  JOIN signups s ON rr.user_id = s.user_id 

  WHERE rr.accept_ts IS NOT NULL 

  GROUP BY  s.user_id 

), 

user_ride_completed AS ( 

  SELECT 

    COUNT(DISTINCT rr.ride_id) num_com, 

    s.user_id 

   FROM ride_requests rr 

  JOIN signups s ON rr.user_id = s.user_id 

  WHERE rr.dropoff_ts IS NOT NULL 

  GROUP BY  s.user_id 

), 

payment AS ( 

  SELECT 

  COUNT(DISTINCT tr.ride_id) num_tr, 

  s.user_id 

  FROM transactions tr 

  LEFT JOIN ride_requests rr ON tr.ride_id = rr.ride_id 

  LEFT JOIN signups s ON rr.user_id = s.user_id 

  GROUP BY  s.user_id   

), 

review_users AS ( 

  SELECT 

    reu.user_id, 

    COUNT(DISTINCT reu.ride_id) num_re 

  FROM reviews reu 

  GROUP BY reu.user_id  

), 

totals AS ( 

  SELECT 

  	dl.platform, 

  	(dl.download_ts :: DATE) download_dt, 

  	su.age_range, 

  	COUNT(dl.app_download_key) AS total_users_downloaded, 

        COUNT(su.user_id) AS total_users_signedup, 

    	COUNT(urs.user_id) AS total_users_ride_requested, 

        COUNT(drac.user_id) AS total_users_driver_accept, 

        COUNT(urc.user_id) AS total_users_ride_completed, 

        COUNT(pay.user_id) AS total_users_payment, 

        COUNT(reu.user_id) AS total_users_review, 

        SUM(urs.num_req) AS total_ride_requested, 

        SUM(drac.num_ac) AS total_driver_accept, 

        SUM(urc.num_com) AS total_ride_completed, 

        SUM(pay.num_tr) AS total_ride_payment, 

        SUM(reu.num_re) AS total_ride_review 

  FROM   downloads dl 

  LEFT JOIN  

    signedup_users su ON dl.app_download_key = su.session_id 

  LEFT JOIN  

    user_ride_requested urs ON su.user_id = urs.user_id  

  LEFT JOIN  

    driver_accepted drac ON su.user_id = drac.user_id 

  LEFT JOIN  

    user_ride_completed urc ON su.user_id = urc.user_id 

  LEFT JOIN  

    payment pay ON su.user_id = pay.user_id 

  LEFT JOIN  

    review_users reu ON su.user_id = reu.user_id 

  GROUP BY  dl.platform, dl.download_ts, su.age_range 

), 

funnel_stages AS ( 

  SELECT  

    0 AS funnel_step, 

    'downloads' AS funnel_name, 

     tot.platform, 

     tot.age_range, 

     tot.download_dt, 

     total_users_downloaded AS user_count, 

     CAST (NULL AS INT) AS ride_count 

  FROM totals tot 

  UNION 

  SELECT 

    1 AS funnel_step, 

  	'signups' AS funnel_name, 

  	tot.platform, 

  	tot.age_range, 

  	tot.download_dt, 

  	total_users_signedup AS user_count, 

   CAST (NULL AS INT) AS ride_count 

  FROM totals tot 

  UNION 

  SELECT 

    2 AS funnel_step, 

    'ride_requested' AS funnel_name, 

     tot.platform, 

     tot.age_range, 

     tot.download_dt, 

     total_users_ride_requested AS user_count, 

     total_ride_requested AS ride_count 

  FROM totals tot 

  UNION 

  SELECT 

    3 AS funnel_step, 

    'driver_accept' AS funnel_name, 

  	tot.platform, 

  	tot.age_range, 

  	tot.download_dt, 

  	total_users_driver_accept AS user_count, 

    total_driver_accept AS ride_count 

  FROM totals tot 

  UNION 

  SELECT 

    4 AS funnel_step, 

    'completed_ride' AS funnel_name, 

  	tot.platform, 

  	tot.age_range, 

  	tot.download_dt, 

  	total_users_ride_completed AS user_count, 

    total_ride_completed AS ride_count 

  FROM  totals  tot 

  UNION 

  SELECT 

    5 AS funnel_step, 

    'payment' AS funnel_name, 

     tot.platform, 

     tot.age_range, 

     tot.download_dt, 

    total_users_payment AS user_count, 

    total_ride_payment AS ride_count 

  FROM totals tot 

  UNION 

  SELECT 

    6 AS funnel_step, 

    'reviews' AS funnel_name, 

    tot.platform, 

    tot.age_range, 

   tot.download_dt, 

   total_users_review AS user_count, 

   total_ride_review AS ride_count 

FROM  totals tot 

) 

SELECT  

  * 

FROM funnel_stages 

;
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

## Hourly Ride Request: 
- Ride requests throughout the day depicts 1st peak period between 8 am and 10 am  and the next peak period in the evening(16:00 - 20:00)hrs
  
```sql
SELECT 
EXTRACT(HOUR FROM rr.request_ts) hours,
COUNT(rr.ride_id) number_of_ride_request
FROM ride_requests rr
GROUP BY EXTRACT(HOUR FROM rr.request_ts)
ORDER BY hours
;
```
 ![](hourly_ride_request.png)

## Rating Segments:
- 50.56% rated their experience with the metro car app as above average(4-5)while 39.2% rated the app as either 1 or 2

```sql
SELECT
    CASE
        WHEN rating BETWEEN 1 AND 2 THEN '1-2 (Below Average)'
        WHEN rating = 3 THEN '3 (Average)'
        WHEN rating BETWEEN 4 AND 5 THEN '4-5 (Above Average)'
        ELSE 'Unknown'
    END AS rating_description,
    COUNT(driver_id) AS driver_count
FROM reviews
GROUP BY rating_description
ORDER BY rating_description
;
 ```
![](rating.png)

## links to Tableau dashboard and Tableau Story
- [Tableau Dashboard](https://public.tableau.com/views/MetroCarDashboard/MetroCarDashboard?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)
- [Tableau Story](https://public.tableau.com/views/TableauStory-FunnelAnalysisMetroCar/TableauStory-FunnelAnalysisatMetroCar?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link) 
## Recommendations:
- 1. Although there is a healthy conversion rate across platforms there is room for improvement which could be achieved by offering incentives to users and drivers based on performance.
- 2. There's a notable drop-off between ride requests and driver acceptance, suggesting a need to recruit more trained drivers.
- 3. Our target demographics for the advert should be the 35-44 age group because they represent the working class and would commute often within the peak periods.
- 4. Implementing price surges within observed peak periods in the morning and evening would be an avenue to optimize profits.

## Future Analysis Ideas:
- Predominantly used vehicle type in metro car and location of use
- Driver's age group and gender
- Which mobile network connects the most passengers to metro drivers
- Which advertising strategy is most effective in terms of accessing our target demographics
