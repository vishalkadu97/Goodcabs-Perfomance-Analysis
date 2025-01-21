use Codebasic
--1. Top and Bottom Performing Cities
--Identify the top 3 and bottom 3 cities by total trips over the entire analysis period

--Top 3 Cities by total trip
Select top 3 ct.city_name, count(tr.trip_id) as Total_Trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Total_Trips desc

go

--Bottom 3 Cities by total trip
Select top 3 ct.city_name, count(tr.trip_id) as Total_Trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Total_Trips 


go
--2.Average fare per trip by City
--Calculate the average fare per trip for each city and 
--compare it with the city's average trip distance.
--Identify the cities with the highest and lowest average
--fare per trip to assess pricing efficiency across locations.

--Average fare per trip for each city and city's average trip distance
Select  ct.city_name, AVG(tr.fare_amount) as Avg_Fare_per_Trip,
AVG(tr.distance_travelled_km) as Avg_distance_per_Trip
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_Fare_per_Trip desc

--cities with the highest average fare per trip
Select Top 1 ct.city_name, AVG(tr.fare_amount) as Avg_Fare_per_Trip,
AVG(tr.distance_travelled_km) as Avg_distance_per_Trip
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_Fare_per_Trip desc

go

--cities with the lowest average fare per trip
Select Top 1 ct.city_name, AVG(tr.fare_amount) as Avg_Fare_per_Trip,
AVG(tr.distance_travelled_km) as Avg_distance_per_Trip
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_Fare_per_Trip

go

--3.Average ratings by city and passanger type
--calculate the average passenger and driver ratings for each city,
--segmented by passenger type(new vs repeat). Identify cities with the highest and lowest ratings.

--average passenger and driver ratings for each city 
Select  ct.city_name, AVG(passenger_rating) as Avg_passenger_rating,
AVG(driver_rating) as Avg_driver_rating
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_passenger_rating desc

go
--segmented by passenger type(new vs repeat)
Select  ct.city_name,tr.passenger_type, AVG(passenger_rating) as Avg_passenger_rating,
AVG(driver_rating) as Avg_driver_rating
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name, tr.passenger_type
order by Avg_passenger_rating desc

go

--cities with the highest ratings
Select top 1  ct.city_name, AVG(passenger_rating) as Avg_passenger_rating,
AVG(driver_rating) as Avg_driver_rating
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_passenger_rating desc

go
--cities with the lowest ratings
Select top 1 ct.city_name, AVG(passenger_rating) as Avg_passenger_rating,
AVG(driver_rating) as Avg_driver_rating
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by ct.city_name
order by Avg_passenger_rating 

go
--ratings by passenger type(new vs repeat)
Select  tr.passenger_type, AVG(passenger_rating) as Avg_passenger_rating,
AVG(driver_rating) as Avg_driver_rating
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
group by tr.passenger_type
order by Avg_passenger_rating desc

go

--4.Peak and low demand months by city
--for each city, identify with the month highest total trips( peak demand) 
--and the month with the lowest total trips(low demand). This analysis will help
--Goodcabs understand seasonal patterns and adjust resources accordingly.

--Peak demand months by city
with CityMonthTrips as (
select ct.city_name,dt.month_name, count(tr.trip_id) as total_trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
join dim_date dt
on tr.date =dt.date
group by ct.city_name,dt.month_name
),
rankedTrips as (
select 
city_name,month_name,total_trips,
ROW_NUMBER() over(partition by city_name order by total_trips desc) as rank
from CityMonthTrips
)

select city_name,
month_name as Month_highest_total_trips,
total_trips
from rankedTrips
where rank =1

go
--low demand months by city

with CityMonthTrips as (
select ct.city_name,dt.month_name, count(tr.trip_id) as total_trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
join dim_date dt
on tr.date =dt.date
group by ct.city_name,dt.month_name
),
rankedTrips as (
select 
city_name,month_name,total_trips,
ROW_NUMBER() over(partition by city_name order by total_trips) as rank
from CityMonthTrips
)

select city_name,
month_name as Month_highest_total_trips,
total_trips
from rankedTrips
where rank =1

go


--5.Weekend vs weekday Trip  Demand by city
--Compare the total trip taken on weekdays vs weekends for each city
--over the six-month period.Identify cities with a strong preference for either
--weekday or weekend trips to understand demand variations.

--total trip taken on weekdays vs weekends for each city

select ct.city_name,dt.day_type, COUNT(tr.trip_id) as Total_trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
join dim_date dt
on tr.date = dt.date
group by ct.city_name, dt.day_type
order by city_name


go

--cities with a strong preference for either weekday or weekend trips

select ct.city_name,dt.day_type, COUNT(tr.trip_id) as Total_trips
from dim_city ct
join fact_trips tr
on ct.city_id = tr.city_id
join dim_date dt
on tr.date = dt.date
group by ct.city_name, dt.day_type
order by Total_trips desc

go

--6.Repeat passenger Frequency and City  Contribution analysis
--Analyse the frequency of trips taken by repeat passengers in each city
--(e.g.,% of repeat  passengers taking 2 trips, 3 trips, etc).
--Identify  which cities  contribute most to higher trip frequencies among  repeat passengers, and
--examine if there  are distinguishable patterns between tourism-focused and business focused cities

--frequency of trips taken by repeat passengers in each city

		SELECT 
    ct.city_name,
    rpt.trip_count AS Frequency,
    SUM(rpt.repeat_passenger_count) AS total_repeat_passenger_count,
    -- Calculate percentage using SUM()
    (SUM(rpt.repeat_passenger_count) * 100.0) / 
        SUM(SUM(rpt.repeat_passenger_count)) OVER (PARTITION BY ct.city_name) AS Percentage
FROM 
    dim_city ct 
JOIN 
    dim_repeat_trip_distribution rpt
    ON ct.city_id = rpt.city_id
GROUP BY 
    ct.city_name, rpt.trip_count
ORDER BY 
    ct.city_name, rpt.trip_count DESC;

go

--cities  contribute most to higher trip frequencies among  repeat passengers
SELECT 
    ct.city_name,
    rpt.trip_count AS Frequency,
    SUM(rpt.repeat_passenger_count) AS total_repeat_passenger_count,
    (SUM(rpt.repeat_passenger_count) * 100.0) / 
        SUM(SUM(rpt.repeat_passenger_count)) OVER (PARTITION BY ct.city_name) AS Percentage
FROM 
    dim_city ct 
JOIN 
    dim_repeat_trip_distribution rpt
    ON ct.city_id = rpt.city_id
GROUP BY 
    ct.city_name, rpt.trip_count
ORDER BY 
    Percentage DESC,ct.city_name, rpt.trip_count;

go

--distinguishable patterns between tourism-focused and business focused cities
--ALTER TABLE dim_city
--ADD  city_type NVARCHAR(50);
--go
--UPDATE dim_city
--SET city_type = CASE 
--    WHEN city_id IN ('AP01', 'KA01', 'KL01', 'RJ01') THEN 'Tourism'
--    WHEN city_id IN ('CH01', 'GJ01', 'GJ02', 'MP01', 'TN01', 'UP01') THEN 'Business'
--    ELSE 'Unknown'
--END;

--go
SELECT 
    ct.city_name,ct.city_type,
    SUM(rpt.repeat_passenger_count) AS total_repeat_passenger_count,
    cast((SUM(rpt.repeat_passenger_count) * 100.0) / 
        SUM(SUM(rpt.repeat_passenger_count)) OVER (PARTITION BY ct.city_type) as decimal(10,2)) AS Percentage
FROM 
    dim_city ct 
JOIN 
    dim_repeat_trip_distribution rpt
    ON ct.city_id = rpt.city_id
GROUP BY 
    ct.city_name,ct.city_type
ORDER BY 
    Percentage DESC,ct.city_name;

	go

	 --total trips by city type
	SELECT 
    ct.city_type,
    SUM(rpt.repeat_passenger_count) AS total_repeat_passenger_count,
    cast(sum(rpt.repeat_passenger_count)*100.0 / sum(sum(rpt.repeat_passenger_count))
	over() as decimal(10,2)) as percentage 
  
FROM 
    dim_city ct 
JOIN 
    dim_repeat_trip_distribution rpt
    ON ct.city_id = rpt.city_id
GROUP BY 
    ct.city_type
ORDER BY 
    Percentage DESC;

	go


--7.Monthly Target Achievement Analysis for Key Metrics
--For each city,evaluable monthly performance against targets for total trips,
--new passengers,and average passenger ratings from targets_db. Determine if each
--metric met, exceeded,or missed the target, and calculate the percentage difference.
--Identify any consistent patterns in target achievement, particularly across tourism
 --versus business-focussed cities.

 --For each city monthly performance against targets for total trips
 with Total_trips_target as (
		Select 
				ct.city_name,
				month(dt.date)as Month,
				dt.month_name,
				mtt.total_target_trips as monthly_target,
				count(tr.trip_id) as Total_trip
				
		from	dim_city ct
		join	fact_trips tr on ct.city_id =tr.city_id
		join	dim_date dt on dt.date = tr.date
		join	monthly_target_trips mtt on tr.city_id =mtt.city_id
		group by 
				ct.city_name,
				month(dt.date),
				dt.month_name,
				mtt.total_target_trips
				)

		select
				ttr.city_name,
				ttr.month_name,
				ttr.monthly_target,
				ttr.Total_trip,
				case 
					when ttr.Total_trip = ttr.monthly_target then 'Trip Target Met' 
					when ttr.Total_trip > ttr.monthly_target then 'Trip Target Exceeded'
					else 'Missed the Target'
				end as Monthly_Target_Status,
				cast((ttr.Total_trip - ttr.monthly_target)*100.0/ttr.monthly_target as decimal(10,2)) as Percentage_difference
		from Total_trips_target ttr
		order by ttr.city_name,ttr.Month
			
			go


					
	--For each city monthly performance against targets for new passengers
	with Total_trips_target as (
		Select 
				ct.city_name,
				month(dt.date)as Month,
				dt.month_name,
				mtnp.target_new_passengers as monthly_target_NewPass,
				count(tr.passenger_type) as Total_NewPass_trip
				
		from	dim_city ct
		join	fact_trips tr on ct.city_id =tr.city_id
		join	dim_date dt on dt.date = tr.date
		join	monthly_target_new_passengers mtnp on tr.city_id =mtnp.city_id
		where tr.passenger_type = 'new'
		group by 
				ct.city_name,
				month(dt.date),
				dt.month_name,
				mtnp.target_new_passengers
				)

		select
				ttr.city_name,
				ttr.month_name,
				ttr.monthly_target_NewPass,
				ttr.Total_NewPass_trip,
				case 
					when ttr.Total_NewPass_trip = ttr.monthly_target_NewPass then 'Trip Target Met' 
					when ttr.Total_NewPass_trip > ttr.monthly_target_NewPass then 'Trip Target Exceeded'
					else 'Missed the Target'
				end as Monthly_Target_Status,
				cast((ttr.Total_NewPass_trip - ttr.monthly_target_NewPass)*100.0/ttr.monthly_target_NewPass as decimal(10,2)) as Percentage_difference

		from Total_trips_target ttr
		order by ttr.city_name,ttr.Month

		go

--For each city monthly performance against targets average passenger ratings


			Select 
			ct.city_name,
			month(dt.date) as month,
			dt.month_name,
			ctpr.target_avg_passenger_rating,
			AVG(tr.passenger_rating) as Avg_Passenger_rating,
			case 
			when AVG(tr.passenger_rating)= ctpr.target_avg_passenger_rating then 'Rating Target Met'
			when AVG(tr.passenger_rating)> ctpr.target_avg_passenger_rating then 'Rating Target Exceeded'
			Else 'Missed the Target'
			End as Passenger_avg_Rating_Target_Status,
			cast((AVG(tr.passenger_rating) - ctpr.target_avg_passenger_rating)*100.0/ctpr.target_avg_passenger_rating as decimal(10,2)) as Percentage_difference

			from fact_trips tr 
			join dim_date dt on dt.date = tr.date
			join dim_city ct on ct.city_id = tr.city_id
			join city_target_passenger_rating ctpr on ct.city_id = ctpr.city_id
			group by ct.city_name,ctpr.target_avg_passenger_rating,dt.month_name,month(dt.date)
			order by ct.city_name,month
			
			go
				
 --tourism versus business-focussed cities avg passenger ratings target status
	Select 
			ct.city_type,
			month(dt.date) as month,
			dt.month_name,
			cast(AVG(ctpr.target_avg_passenger_rating)as decimal(10,2))AS Avg_Target_passenger_rating,
			AVG(tr.passenger_rating) as Avg_Passenger_rating,
			case 
			when AVG(tr.passenger_rating)= AVG(ctpr.target_avg_passenger_rating) then 'Rating Target Met'
			when AVG(tr.passenger_rating)> AVG(ctpr.target_avg_passenger_rating) then 'Rating Target Exceeded'
			Else 'Missed the Target'
			End as Passenger_avg_Rating_Target_Status,
			cast((AVG(tr.passenger_rating) - AVG(ctpr.target_avg_passenger_rating))*100.0/AVG(ctpr.target_avg_passenger_rating) as decimal(10,2)) as Percentage_difference

			from fact_trips tr 
			join dim_date dt on dt.date = tr.date
			join dim_city ct on ct.city_id = tr.city_id
			join city_target_passenger_rating ctpr on ct.city_id = ctpr.city_id
			group by ct.city_type,dt.month_name,month(dt.date)
			order by month,ct.city_type	
	
	go


--8.Highest and Lowest Repeat Passenger rate (RPR%) by City and Month
--8.1 Analyse the Repeat Passenger Rate (RPR%) for each city across the six month period
--Identify the top 2 and bottom 2 cities based on RPR% to determine which locations have 
--the strongest and weakest rates
--8.2 Similarly, analyse the RPR% by month across all cities and identify the  months with
--the highest and lowest repeat passenger rates. This will help  to pinpoint any seasonal
--patterns or months with the higher  repeat passenger loyalty.

--8.1 Analyse the Repeat Passenger Rate (RPR%) for each city across the six month period
--Identify the top 2 and bottom 2 cities based on RPR% to determine which locations have 
--the strongest and weakest rates

 --Repeat Passenger Rate (RPR%) for each city across the six month period
select 
	ct.city_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id
group by ct.city_name

go

--top 2 cities based on RPR%
select top 2
	ct.city_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id
group by ct.city_name
order by "RPR%" desc

go

--bottom 2 cities based on RPR%
select top 2
	ct.city_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id
group by ct.city_name
order by "RPR%"

go

--8.2 Similarly, analyse the RPR% by month across all cities and identify the  months with
--the highest and lowest repeat passenger rates. This will help  to pinpoint any seasonal
--patterns or months with the higher  repeat passenger loyalty.

--RPR% by month across all cities

select 
	Month(ps.month) as Month,
	dt.month_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id 
join dim_date dt on dt.date=ps.month
group by 	dt.month_name,
			Month(ps.month)
order by Month

go


--months with the highest repeat passenger rates
select Top 1
	Month(ps.month) as Month,
	dt.month_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id 
join dim_date dt on dt.date=ps.month
group by 	dt.month_name,
			Month(ps.month)
order by "RPR%" desc

go

--months with the lowest repeat passenger rates
select Top 1
	Month(ps.month) as Month,
	dt.month_name,
	sum(ps.repeat_passengers) as repeat_passenger,
	sum(ps.total_passengers)as total_passenger,
	cast(sum(ps.repeat_passengers)*100.0/sum(ps.total_passengers) as decimal(10,2)) as "RPR%"
	
from 
	dim_city ct
join fact_passenger_summary ps on ct.city_id = ps.city_id 
join dim_date dt on dt.date=ps.month
group by 	dt.month_name,
			Month(ps.month)
order by "RPR%" 
