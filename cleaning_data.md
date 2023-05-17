What issues will you address by cleaning the data?
The issues I wanted to address were to remove duplicate data, remove mostly and completely empty columns, or columns that had the same result across the majority, replace "(not set)" and other kinds of pointless strings with NULLs and convert times and prices into the correct units. 

Queries:
Below, provide the SQL queries you used to clean your data.

Deleting duplicate data from the analytics table (The same code structure would be used for the other tables as well by replacing column and table names)
DELETE FROM analytics
WHERE (fullvisitorid, visitid, visitnumber, unit_price) IN (
  SELECT fullvisitorid, visitid, visitnumber, unit_price
  FROM analytics
  GROUP BY fullvisitorid, visitid, visitnumber, unit_price
  HAVING COUNT(*) > 1
);

Deleting specific columns from tables
alter table all_sessions
drop column sessionqualitydim

Updating time on site column (assuming seconds so transforming into minutes)
update all_sessions set timeonsite = timeonsite / 60

Updating string values to NULLs
update all_sessions
set country = null
where country = '(not set)'

Changing and updating the ratio column in sales_report into a percentage and adjusting decimals
update sales_report set ratio = ratio * 100
alter table sales_report alter column percentageofstocksold type decimal(10,2)

Updating unit_price
update analytics set unit_price = unit_price / 1000000
alter table analytics alter column unit_price type decimal(10,2)

Removing spaces (LTRIM for spaces before string, TRIM for spaces across entire string)
update all_sessions set productsku = trim(productsku)

Changing data  (Could not manipulate column with wrong data type set)
alter table analytics alter column visitid set data type character varying
