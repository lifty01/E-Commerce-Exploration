Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
select alls.country, alls.city, sum((sbs.total_ordered * an.unit_price)) as total_revenue
from all_sessions alls
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
where sbs.total_ordered is not null and an.unit_price is not null
group by alls.country, alls.city
order by total_revenue desc
limit 10

Answer:
The United States as a whole (without city specifications) has the highest level of transaction revenues on the site. When it comes to specific cities however, Mountain View in the United States is the city with the highest level of transaction revenues on the site.


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
select alls.country, alls.city, cast(AVG(sbs.total_ordered) as decimal (10,0)) as average 
from sales_by_sku sbs
inner join all_sessions alls
on alls.productsku = sbs.productsku
where sbs.total_ordered is not null and alls.country is not null
group by country, city
order by country asc

Answer:
The answer for Albania on average 50 products ordered on the site. The next city available on the list is Buenos Aires with an average of 30 products ordered.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
select alls.country, alls.city, alls.v2productcategory, sbs.total_ordered
from all_sessions alls
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
where total_ordered is not null
group by alls.country, alls.city, alls.v2productcategory, sbs.total_ordered
order by total_ordered desc


Answer:
It would seem as if the type of product category that is most ordered are fall under the Home/Office category, followed by the Home/Drinkware category.


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
select country, city, v2productname, total_ordered from (
	select 
		country, 
		city, 
		v2productname, 
		total_ordered,
	 	row_number() over(partition by country,city order by total_ordered desc) rn
	 from all_sessions alls
	 inner join sales_by_sku sbs
	 on alls.productsku = sbs.productsku
		where total_ordered is not null and country is not null
	) result_table
where rn = 1
order by total_ordered desc

Answer: The top selling product is a Ballpoint LED light pen, which makes sense given the previous question of most ordered category. The product is the most popular in 22 locations, 10 of which are in the United States.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
select alls.country, alls.city, sum((sbs.total_ordered * an.unit_price)) as total_revenue, v2productname, percentageofstocksold
from all_sessions alls
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
inner join sales_report sr
on sbs.productsku = sr.productsku
where sbs.total_ordered is not null and an.unit_price is not null
group by alls.country, alls.city, v2productname, percentageofstocksold
order by total_revenue desc
limit 10

Answer:
For this question, I found out that the United States spends quite a bit on "Bottle" products, where 5 of the top 10 results include "Bottle" related products ordered by various locations throughout the United States. This also reflects in the percentageofstocksold column, where we can observe quite a dent specifically for a Stainless Steel Sport Bottle. 
