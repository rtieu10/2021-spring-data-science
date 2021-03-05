
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
	```
	SELECT
      *
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
	```

7. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
	``` 
    #count tells us the number of distinct values in the col
    
	SELECT
      COUNT(DISTINCT complaint_description)      
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
	```
  
8. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
	``` 
    #listed under SELECT are the columns we wish to select 
    #group by - group the owning depts by category 
    #order - we order the counts by amount, and in descending order 
    
	SELECT
        owning_department,
        COUNT(owning_department) AS amount
    FROM
        `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY 
         owning_department
    ORDER BY amount DESC 
	```

9. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
	```
	SELECT
      complaint_description,
      COUNT(complaint_description) AS amount
   FROM
      `bigquery-public-data.austin_311.311_service_requests`
   GROUP BY
      complaint_description
   ORDER BY
      amount DESC
   LIMIT
      5
	  ```
10. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
	```
	SELECT
      complaint_description,
      COUNT(complaint_description) AS amount
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
    WHERE
      owning_department = 'Animal Services Office'
    GROUP BY
      complaint_description
    ORDER BY
      amount DESC
    LIMIT
      5
	```

11. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
	```
    #SELECT - indicate the columns to include 
    #FROM - the table we pull data from 
    #GROUPBY - what to organize data by 
    #HAVING - pass a condition, and it returns rows that meet the condition 
    
	SELECT
      unique_key,
      COUNT(unique_key) AS amount
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY
      unique_key
    HAVING
      COUNT(unique_key) > 1
      
    there were no duplicates
	```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
	```
	WITH
      POP_2000 AS(
      SELECT
        zipcode AS zip2000,
        SUM(population) AS population_2000
      FROM
        `bigquery-public-data.census_bureau_usa.population_by_zip_2000`
      GROUP BY
        zipcode ),
        
      #SECOND TABLE 
      
      POP_2010 AS(
      SELECT
        zipcode AS zip2010,
        SUM(population) AS population_2010
      FROM
        `bigquery-public-data.census_bureau_usa.population_by_zip_2010`
      GROUP BY
        zipcode )
        
      #JOIN TABLES
      
      SELECT
          zip2000,population_2000,population_2010
      FROM
          POP_2000
      INNER JOIN
          POP_2010
      ON
          zip2000 = zip2010
	```

### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd. 
	```
	SELECT
      advertiser_name,
      spend_usd
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    ORDER BY
      spend_usd DESC
    LIMIT
      1
	```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
	```
    #SELECT - select the advertiser names, and sum up the money spent (sum_usd)
    #ORDER BY - DESC (from highest to lowest) 
	SELECT
      advertiser_name,
      SUM(spend_usd) AS sum_usd
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY 
      advertiser_name
    ORDER BY
      sum_usd DESC
    LIMIT
      6
      
    SENATE LEADERSHIP FUND 
	```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
	```
	SELECT
      week_start_date,
      spend_usd
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    ORDER BY
      spend_usd DESC
    
    2020-10-18 had the highest weekly spend
	```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only. 
	```
	SELECT
      week_start_date,
        SUM(spend_usd) AS total_spent
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    WHERE
      EXTRACT(month FROM week_start_date) = 8
    GROUP BY
      week_start_date

	```
6.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
	```
    #SELECT - columns we are selecting 
    #COUNT - counting each advertiser 
    #FROM - where pull data rom 
    #WHERE - the condition that must be true 
    #GROUP BY - what the rows are grouped by 
    
	SELECT
      advertiser_name,
      COUNT(advertiser_name) AS advertiser
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    WHERE
      advertiser_name = "TOM STEYER 2020"
    GROUP BY
      advertiser_name

    50 advertisements
	```
7. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
	```
		[YOUR QUERY HERE]
	```
8. For each advertiser_name, find the average spend per ad. 
	```
	SELECT
      advertiser_name,
      AVG(spend_usd) as avg_spent
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY
      advertiser_name
	```
10. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
	``` 
	WITH
     T AS(
         SELECT
           advertiser_name,
           AVG(spend_usd) AS avg_spent
         FROM
           `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
         GROUP BY
            advertiser_name
         ORDER BY
            avg_spent ASC )

      #filter for averages greater than 0 
  
      SELECT * FROM T
      WHERE
        avg_spent > 0
        
     BRANDY K CHAMBERS 
	```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
	```
	SELECT
      gender,
      COUNT(gender) AS gender_count
    FROM
      `bigquery-public-data.new_york_citibike.citibike_trips`
    GROUP BY
      gender
      
    Males went on more bike trips
	```
2. What was the average, shortest, and longest bike trip taken in minutes?
	```
	SELECT
      AVG(tripduration)/60 AS avg_time,
      MIN(tripduration)/60 AS shortest_trip,
      MAX(tripduration)/60 AS longest_trip
    FROM
      `bigquery-public-data.new_york_citibike.citibike_trips`
	```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
	```
	WITH
      T_STARTED AS(
      SELECT
        start_station_name,
        COUNT(start_station_name) AS start_station_count
      FROM
        `bigquery-public-data.new_york_citibike.citibike_trips`
      GROUP BY
        start_station_name ),
        
      #SECOND TABLE 

      T_ENDED AS(
      SELECT
        end_station_name,
        COUNT(end_station_name) AS end_station_count
      FROM
        `bigquery-public-data.new_york_citibike.citibike_trips`
      GROUP BY
        end_station_name )

    SELECT
      start_station_name,
      start_station_count,
      end_station_count
    FROM T_STARTED
    INNER JOIN T_ENDED ON
      start_station_name = end_station_name
	```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 
	* YOUR LINK:  ________________________________
9. Complete the two questions in the colab notebook file. 
