

# SQL Taks


The task is solved using a Docker container of Oracle database and DBeaver.

```console 

docker run --name oracl_db -d -p 7070:1521 -e ORACLE_PASSWORD=secret -e ORACLE_DATABASE=sqltask -e APP_USER=aram -e APP_USER_PASSWORD=secret gvenzl/oracle-xe

```
    
1. Find duplicate records in RPM_FUTURE_RETAIL. The record is a duplicate if there already such item/location/action_date combination exists (prices can differ):


#### Solution: 

 - a. Write a query to return all duplicated records. 

``` sql 
SELECT LOCATION, ITEM, ACTION_DATE, COUNT(FUTURE_RETAIL_ID) AS number_of_dupl  
	FROM RPM_FUTURE_RETAIL 
	GROUP BY LOCATION, ITEM, ACTION_DATE 
	HAVING COUNT(FUTURE_RETAIL_ID) > 1
  ```
  - b. Delete duplicate records, so that item/location/action_date will be unique.
  
  ``` sql  DELETE FROM RPM_FUTURE_RETAIL 
	WHERE FUTURE_RETAIL_ID NOT IN (
	SELECT max(FUTURE_RETAIL_ID) 
	FROM RPM_FUTURE_RETAIL 
	GROUP BY item, LOCATION, ACTION_DATE) 
  ```

2. Write a query to find all item/zone combinations in rpm_zone_future_retail for which there are no pricing data exists at the location level (rpm_future_retail).



    First approach: Create a **View** by joining RPM_FUTURE_RETAIL and RPM_ZONE_LOCATION, then join the view and RPM_ZONE_FUTURE_RETAIL to filter items for which pricing data does not exist in RPM_FUTURE_RETAIL.

``` sql 
CREATE VIEW temp AS 
SELECT rf.ITEM, rf.LOCATION, RZL.ZONE_ID 
FROM RPM_FUTURE_RETAIL rf
JOIN RPM_ZONE_LOCATION rzl ON rf.LOCATION = RZL.LOCATION 


SELECT r.ZONE_FUTURE_RETAIL_ID, r.ITEM, r.ZONE, r.ACTION_DATE, r.SELLING_RETAIL 
FROM RPM_ZONE_FUTURE_RETAIL r 
LEFT JOIN TEMP t ON r.ITEM = t.ITEM AND r.ZONE = t.ZONE_ID 
WHERE t.ITEM IS null
```

  Second approach: Use **nested** query instead of creating view.


``` sql 

SELECT r.ZONE_FUTURE_RETAIL_ID, r.ITEM, r.ZONE, r.ACTION_DATE, r.SELLING_RETAIL
FROM RPM_ZONE_FUTURE_RETAIL r
LEFT JOIN (
  SELECT DISTINCT ITEM, ZONE_ID
  FROM RPM_FUTURE_RETAIL fr
  JOIN RPM_ZONE_LOCATION zl ON fr.LOCATION = zl.LOCATION
) t ON r.ITEM = t.ITEM AND r.ZONE = t.ZONE_ID
WHERE t.ITEM IS NULL
```

3. Write a query that will return current and previous selling retail prices (preceding action_date) for each item/location combination and the difference between current and previous prices. Below is an example of the result set:

``` sql 
SELECT 
    item, 
    location, 
    selling_retail AS current_price, 
    LAG(selling_retail) OVER (PARTITION BY item, location ORDER BY action_date) AS previous_price,
    selling_retail - LAG(selling_retail) OVER (PARTITION BY item, location ORDER BY action_date) AS price_difference
FROM RPM_FUTURE_RETAIL rfr  
WHERE action_date <= SYSDATE
ORDER BY item, location, action_date DESC;
```




4.  Assuming that the current date in the system is today
     Find the price for each item at each location on the current date. Since there are many prices for the same item/location combination, use the selling_retail values closest to the current  date, meaning latest action_date which is  <= current date).

``` sql 
SELECT r.ITEM, r.LOCATION, r.SELLING_RETAIL
FROM RPM_FUTURE_RETAIL r
WHERE r.ACTION_DATE = (
  SELECT MAX(ACTION_DATE)
  FROM RPM_FUTURE_RETAIL
  WHERE ITEM = r.ITEM AND LOCATION = r.LOCATION AND ACTION_DATE <= SYSDATE 
) 
```