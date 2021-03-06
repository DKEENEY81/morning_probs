# Queries on the Taxi Trip Database

#### The Taxi Trip database contains various information about individual trips taken in taxis. It contains a table called taxi_trips and the structure is below

```sql
CREATE TABLE taxi_trips (
    trip_id bigserial PRIMARY KEY,
    vendor_id varchar(1) NOT NULL,
    tpep_pickup_datetime timestamp with time zone NOT NULL,
    tpep_dropoff_datetime timestamp with time zone NOT NULL,
    passenger_count integer NOT NULL,
    trip_distance numeric(8,2) NOT NULL,
    pickup_longitude numeric(18,15) NOT NULL,
    pickup_latitude numeric(18,15) NOT NULL,
    rate_code_id varchar(2) NOT NULL,
    store_and_fwd_flag varchar(1) NOT NULL,
    dropoff_longitude numeric(18,15) NOT NULL,
    dropoff_latitude numeric(18,15) NOT NULL,
    payment_type varchar(1) NOT NULL,
    fare_amount numeric(9,2) NOT NULL,
    extra numeric(9,2) NOT NULL,
    mta_tax numeric(5,2) NOT NULL,
    tip_amount numeric(9,2) NOT NULL,
    tolls_amount numeric(9,2) NOT NULL,
    improvement_surcharge numeric(9,2) NOT NULL,
    total_amount numeric(9,2) NOT NULL
```

#### Let's look at some questions dealing with the taxi database


# !challenge

* type: code-snippet
* language: sql
* id: 24ca18aa-db61-4054-899f-a7756114bcf7
* title: Average Trip Distance by vendor
* data_path: /DATA/taxis.sql



##### !question
Return the average distance traveled per trip by each of the vendors in taxi_trips. Round your answers to 2 decimals.
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select vendor_id, round(avg(trip_distance),2) from taxi_trips
group by vendor_id;
##### !end-tests


### !hint
group by vendor
### !end-hint


#### !explanation
```sql
Select vendor_id, round(avg(trip_distance),2) from taxi_trips
group by vendor_id;
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: 946c2c4e-e2b8-4854-a3f9-927b4d3e038a
* title: Average Speed of Driver per Trip
* data_path: /DATA/taxis.sql



##### !question
Using pickup and dropoff times to calculate the length of time of each trip, as well as the distance traveled, calculate and return the speed of the driver in MPH for each trip, rounded to 2 decimal places. Exclude rows where time or distance is 0. Only 1 column is returned.
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select round((trip_distance / extract(epoch from (tpep_dropoff_datetime - tpep_pickup_datetime))*3600)::numeric,2) as mph
from taxi_trips
where trip_distance !=0 and extract(epoch from (tpep_dropoff_datetime - tpep_pickup_datetime)) !=0;

##### !end-tests


### !hint
When you subtract the times, you get an interval, use: select extract(epoch from (tpep_dropoff_datetime - tpep_pickup_datetime)) to get the time in seconds. Distance traveled (in miles) is given, so just divide distance by time to get speed, but don't forget to convert back to MPH.
### !end-hint

### !hint
You need to exclude rows where either distance or time traveled is 0.
### !end-hint

### !hint
You'll need to cast your double precision data to numeric before rounding. The speed calculation you did gives double precision data.
### !end-hint

#### !explanation
```sql
Select round((trip_distance / extract(epoch from (tpep_dropoff_datetime - tpep_pickup_datetime))*3600)::numeric,2) as mph
from taxi_trips
where trip_distance !=0 and extract(epoch from (tpep_dropoff_datetime - tpep_pickup_datetime)) !=0
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: 483b8f3d-6016-439b-8a24-b8604c115c43
* title: Average Cost/Rider for Multi-Rider trips
* data_path: /DATA/taxis.sql



##### !question
Calculate the average cost per rider for all trips where there was more than 1 rider. Round the answer to 2 decimal places (the answer is one average for all riders on these trips)
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select round(avg(total_amount / passenger_count),2)
from taxi_trips
where passenger_count>1;
##### !end-tests


### !hint

### !end-hint


#### !explanation
```sql
Select round(avg(total_amount / passenger_count),2)
from taxi_trips
where passenger_count>1
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: cfdafbe7-4439-4856-bd5d-195f5179ba4f
* title: Highest Tip % by rate code
* data_path: /DATA/taxis.sql



##### !question
Find the highest tip percentage (use fare amount as denominator) for each rate code, order by rate_code ascending. Return rate_code_id and the percentage.	
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select rate_code_id, max(tip_amount/fare_amount)
from taxi_trips
where fare_amount !=0
group by rate_code_id
order by rate_code_id
##### !end-tests


### !hint
where fare amount is not 0
### !end-hint


#### !explanation
```sql
Select rate_code_id, max(tip_amount/fare_amount)
from taxi_trips
where fare_amount !=0
group by rate_code_id
order by rate_code_id
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: d83a1786-31d1-4ba4-a979-24ee15c841aa
* title: Show all trip info where tip % was highest for its rate code
* data_path: /DATA/taxis.sql



##### !question
Return all columns for the trips that had the highest tip percentage in their rate code as found above
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select a.* from taxi_trips a
inner join (Select rate_code_id, max(tip_amount/fare_amount) as high
			from taxi_trips
			where fare_amount !=0
			group by rate_code_id) b
on b.rate_code_id = a.rate_code_id
and b.high = a.tip_amount/a.fare_amount
where fare_amount !=0;
##### !end-tests

### !hint
You can do a join on 2 things
### !end-hint

### !hint
You can use table_name.* to only select columns from one of your tables
### !end-hint

#### !explanation
```sql
Select a.* from taxi_trips a
inner join (Select rate_code_id, max(tip_amount/fare_amount) as high
			from taxi_trips
			where fare_amount !=0
			group by rate_code_id) b
on b.rate_code_id = a.rate_code_id
and b.high = a.tip_amount/a.fare_amount
where fare_amount !=0;
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: b129d770-d45f-49ff-85cb-377fb2e8b99b
* title: Case When
* data_path: /DATA/taxis.sql



##### !question
Return all the original columns as well as a new column called 'duration' that contains "Long" if the trip was over 15miles, and 'Short' otherwise for each trip.
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select *, 
case 
	when trip_distance >15 THEN 'Long'
	ELSE 'Short'
end duration
from taxi_trips

##### !end-tests



### !hint
Don't forget the END in your case when
### !end-hint

#### !explanation
```sql
Select *, 
case 
	when trip_distance >15 THEN 'Long'
	ELSE 'Short'
end duration
from taxi_trips
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: 0cb98eeb-fb40-4f09-a23e-44c1a0e38eea
* title: Revenues by vendor by rate code
* data_path: /DATA/taxis.sql



##### !question
Find the total revenues by rate_code_id for each vendor. Order by vendor ascending and total revenues descending (Columns should be vendor, rate_code, revenues)
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select vendor_id, rate_code_id, sum(fare_amount)
from taxi_trips
group by vendor_id, rate_code_id
order by vendor_id, sum(fare_amount) desc
##### !end-tests


### !hint

### !end-hint

#### !explanation
```sql
Select vendor_id, rate_code_id, sum(fare_amount)
from taxi_trips
group by vendor_id, rate_code_id
order by vendor_id, sum(fare_amount) desc
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: 331230ee-98dc-4866-bd8d-d9e848e1498c
* title: Trips with higher than average fare amounts
* data_path: /DATA/taxis.sql



##### !question
Return all trip information from trips that had a higher than average fare amount
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select * from taxi_trips
where fare_amount > (Select avg(fare_amount) from taxi_trips)
##### !end-tests


### !hint

### !end-hint

#### !explanation
```sql
Select * from taxi_trips
where fare_amount > (Select avg(fare_amount) from taxi_trips)
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: 5c11e149-fcd5-4548-b344-058e87be66ea
* title: Trips with a fare amount within 10pct of the average
* data_path: /DATA/taxis.sql



##### !question
Return all trips that had a fare amount within 10pct of the average fare amount for all the trips.
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
Select * from taxi_trips
where fare_amount between (Select avg(fare_amount)*.9 from taxi_trips) 
and (Select avg(fare_amount)*1.1 from taxi_trips)
##### !end-tests


### !hint
Use between
### !end-hint

#### !explanation
```sql
Select * from taxi_trips
where fare_amount between (Select avg(fare_amount)*.9 from taxi_trips) 
and (Select avg(fare_amount)*1.1 from taxi_trips)
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: beeab2f6-2260-4f37-ae05-427ee5d193c3
* title: Payment type totals
* data_path: /DATA/taxis.sql



##### !question
Return 3 columns for each trip: the fare_amount, the payment_type, and the sum of all the fare_amounts for the payment type of that row. Look up the syntax for a window function (it will create a subtotal by payment_type). Order by descending payment type.
##### !end-question

##### !placeholder

```sql

```

##### !end-placeholder

##### !tests
SELECT fare_amount, payment_type,
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type desc)
FROM taxi_trips
##### !end-tests


### !hint
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type desc)
this should be what your window function is doing
### !end-hint

#### !explanation
```sql
SELECT fare_amount, payment_type,
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type desc)
FROM taxi_trips
```
#### !end-explanation

# !end-challenge
