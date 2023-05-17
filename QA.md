What are your risk areas? Identify and describe them.

QA Process:
Initially, when looking and playing around with the data, I knew that I wanted to remove rows that were entirely duplicated across the entirety of the row, rather than duplicates found in 1 or 2 columns. I also wanted to clear as many of the "irrelevant" columns as possible, so all of the columns that had most, if not all, no or NULL values. For this, I also deleted columns that might have had a few results but when comparing 50 results across in a column with thousands of rows, it didn't really make sense to keep those either. I also replaced "placeholder" values with NULLs, such as "(not set)". And I also converted times and prices to streamline them across the entire table, making sure to add the correct amount of decimals.

Describe your QA process and include the SQL queries used to execute it.

Checking if similarly named columns have matches results
select sr.total_ordered, sbs.total_ordered, p.orderedquantity, an.units_sold, alls.productquantity
from sales_report sr
full outer join sales_by_sku sbs
on sr.productsku = sbs.productsku
full outer join products p
on sr.productsku = p.sku
full outer join all_sessions alls
on p.sku = alls.productsku
full outer join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.productprice = an.unit_price
where sr.total_ordered <> an.units_sold
order by sr.total_ordered asc

select sr.total_ordered, sbs.total_ordered
from sales_report sr
full outer join sales_by_sku sbs
on sr.productsku = sbs.productsku
full outer join all_sessions alls
on sr.productsku = alls.productsku
full outer join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.productprice = an.unit_price
where sr.total_ordered <> sbs.total_ordered
order by sr.total_ordered asc

With these two queries I found that the "total_ordered" colums in the "sales_by_sku" table was the one I most trusted to be of "total products ordered by customers" and that had more available values when comparing the same column to the "sales_report" table. I did the same when comparing productprice and unit_price from the analytics and all_sessions tables.

Checking duplicates from the analytics table (The same query structure was used for most tables and columns. I only had to switch the names of the columns and tables I was checking on.)
select visitnumber, visitid, fullvisitorid, unit_price, count(*) as total
from analytics
group by visitnumber, visitid, fullvisitorid, unit_price
having count(*) > 1

Checking what kind of results/how many different results of a column
select column, count(column) from table
group by column
