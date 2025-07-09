# Amazon-Sales-project-MS_Excel-SQL-PowerBI  

          #Amazon Sales

use pradumandb;
select * from amazon;


#Total shipped quantity and amount per state

select `ship-state`, round(sum(amount),2) as total_amount, sum(Qty) as quantity 
from amazon 
group by `ship-state`
order by total_amount desc;


#Most Popular Product Size and Category

select Category,Size, count(*) as order_count 
from amazon 
where Status like 'Shipped%'
group by Category,Size 
order by Category,Size;


#Cancellation Rate by Fulfillment Type

with rate as (select Fulfilment, 
count(case when `Courier Status` = 'Cancelled' then 1 end) as cancelled, 
count(*) as total_orders
from amazon
group by Fulfilment)

select Fulfilment, cancelled,total_orders,round(cancelled*100.0/ total_orders,2) as cancel_rate 
from rate;  


# top category in each state sold with the highest average amount 

with cet as (select Category,`ship-state`, sum(amount) as total_amount from amazon 
where `Courier Status` = 'Shipped' 
group by Category,`ship-state`),

highest as(select Category,`ship-state`,total_amount,
row_number() over(partition by Category order by total_amount desc) as rnk from cet)

select `ship-state`,Category,total_amount from highest 
where rnk = 1;


#What is the monthly sales 

select category,months,total_amount from (select category, sum(amount) as total_amount,month(Date) as months from amazon 
where `Courier Status` = 'Shipped'
group by category, month(Date)) as a
order by months;



# total number of order cancelled every months 

select category,months,orders from (select category,count(*)as orders,month(Date) as months from amazon 
where `Courier Status` = 'Cancelled'
group by category, month(Date)) as a
order by months,orders;


#count orders of the status 

select `Status`,count(*) as total_orders 
from amazon 
group by `Status`
order by total_orders desc;




#courier Status of orders 

select `Courier Status`,count(*) as orders from amazon 
group by `Courier Status`
order by `Courier Status` desc
  
