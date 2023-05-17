Question 1: What is the top selling product for the month of June, ordered by country/city?

SQL Queries:
select alls.country, alls.city, alls.date, v2productname, sum((sbs.total_ordered * an.unit_price)) as total_revenue
from all_sessions alls
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
where extract(month from alls.date) = 06 and sbs.total_ordered is not null and an.unit_price is not null
group by alls.country, alls.city, alls.date, v2productname
order by total_revenue desc

Answer: The top selling product is a google Spiral Journal with Pen which had the highest generated revenue from Indonesia.

Question 2: What were the total amount of revenue made per month?

SQL Queries:
SELECT extract(YEAR from alls.date) AS Year, extract (month from alls.date) AS Month, sum((sbs.total_ordered * an.unit_price)) AS Total_Sales 
FROM all_sessions alls
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
group by extract(YEAR from alls.date), extract(month from alls.date)

Answer:
May 2017: 2,507,523.66$
June 2017: 2,802,235.90$
July 2017: 2,926,371.03$
August 2017: 58,295.02$


Question 3: Is there a link between the number of time a person has spent on a site during an instance and the amount they spent?

SQL Queries:
select alls.fullvisitorid, alls.visitid, alls.timeonsite, sum(an.unit_price * sbs.total_ordered) as totalpurchaseamount
from all_sessions alls
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
where alls.timeonsite is not null and alls.timeonsite > 0 and an.unit_price is not null and sbs.total_ordered is not null
group by alls.fullvisitorid, alls.visitid, alls.timeonsite
order by timeonsite desc

Answer: No clear link was found. It seems like longer amounts of time spent on a visit instance by a person can yield just as big or small purchase amounts as shorter amounts of time.

Question 4: What items don't seem very interesting to customers based on their city/country?

SQL Queries:
select alls.country, alls.city, alls.v2productname, sum(alls.pageviews) as total_page_views, sbs.total_ordered
from all_sessions alls
inner join analytics an
on alls.fullvisitorid = an.fullvisitorid and alls.visitid = an.visitid
inner join sales_by_sku sbs
on alls.productsku = sbs.productsku
where total_ordered is not null
group by alls.city, alls.country, alls.v2productname, sbs.total_ordered
order by sum(alls.pageviews) desc

Answer:
Looking at the number of total page views for a product and comparing it to the number ordered tells us that while a product might seem interesting at first, it might not be enough to keep customer's interest. One example are the Waze Dress Socks, viewed 385 times but only ordered 1 time.


Question 5: Which products are going to waste?

SQL Queries:
select alls.country, alls.city, sbs.total_ordered, sr.stocklevel, alls.v2productname
from sales_by_sku sbs
inner join sales_report sr
on sbs.productsku = sr.productsku
inner join all_sessions alls
on sr.productsku = alls.productsku
where sbs.total_ordered = 1 and sbs.total_ordered is not null
group by alls.country, alls.city, sbs.total_ordered, sr.stocklevel, alls.v2productname

Answer: T-Shirts
