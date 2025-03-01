# DatingApp_Business_Insights-SQL-
-- Real-World SQL Queries for Dating App Dataset

-- 1. Gender Distribution
select Gender, Count(*) as User_count from Profiles
group by Gender;


-- 2. Average Age of Users by City

select City, avg(age) as Average_age from profiles
group by City
order by avg(age) desc;


-- 3. Users with Children and Their Occupations

select u.UserName, p.Occupation, p.Children from Profiles as p
inner join Users as u on u.user_id = p.user_id
where p.Children >0
order by Children desc;


-- 4. Top 3 Cities with Highest Number of Users
select top 3 city, count(*) as User_count from profiles
Group by city
order by count(*) desc;


-- 5. Age Group Usage Analysis

select case
	when age between 18 and 25 then '18-25'
	when age between 26 and 35 then '26-35'
	when age between 36 and 45 then '36-45'
	when age between 46 and 55 then '46-55'
	else '56+' end as Age_group,
count(*) as User_count
from profiles
group by case
	when age between 18 and 25 then '18-25'
	when age between 26 and 35 then '26-35'
	when age between 36 and 45 then '36-45'
	when age between 46 and 55 then '46-55'
	else '56+' end
order by count(*) desc;

       

-- 6. Top 5 Occupations with Highest Swipe Counts
select top 5 Occupation, sum(Swipe_count) as Total_swipes
From Profiles
group by Occupation
Order By sum(Swipe_count) desc;


-- 7. Users with More Than 300 Swipes in Last 6 Months

select p.User_id, u.Username, p.Swipe_Count
from Profiles as p
inner join Users as u on u.user_id = p.User_id
where p.swipe_count > 300
and u.registration_date > dateadd(month,-6,getdate());



-- 8. Paid Subscribers by Plan Type
select Plan_type, count(*) as Total_Users
from subscriptions 
group by Plan_type
Order by count(*) desc;


-- 9. Revenue by Subscription Plans
Select Plan_type,
sum(case when plan_type = 'silver' then 999 
	when plan_type = 'gold' then 1999 else 0 end) as Revenue
from subscriptions
group by plan_type
order by Revenue desc;


-- 10. Expired Subscriptions in Last 3 Months
select user_id, Plan_type, End_date from subscriptions
where end_date < getdate() 
and end_date >= dateadd(month,-3,getdate());


-- 11. City with Most Paid Subscribers
select p.City, count(s.user_id) as Paid_users
From Subscriptions as s
inner join profiles as p on p.user_id = s.user_id
where s.plan_type in ('silver','gold')
group by p.city
order by count(s.user_id) desc;


-- 12. Most Active User with Maximum Matches
select top 1 m.user1_id,u.Username, count(m.user1_id) as Total_matches
from matches as m
inner join users as u on u.user_id = m.user1_id
group by user1_id, u.Username
order by count(*) desc;



-- 13. Top 3 Interests Leading to Matches

select top 3 p.Interests, count(*) as match_count
from profiles as p
inner join matches as m on p.user_id = m.user1_id
group by p.interests
order by count(*) desc;


-- 14. High-Value Customers
select u.Username, p.Swipe_count, s.Plan_type
from Users as u
inner join profiles as p on u.user_id = p.user_id
inner join subscriptions as s on u.user_id = s.user_id
where s.plan_type = 'Gold' and p.swipe_count > 200;


-- 15. Monthly User Growth Rate
select format(registration_date,'yyyy-MM') as Month, count(*) as New_users
from users
group by format(registration_date,'yyyy-MM')
order by Month;


-- 16. Daily Active Users Percentage
select count(*)*100.0/(select count(*) from users) as Daily_active_percentage
from profiles
where Frequency_of_use = 'Daily';





-- 17. Average Subscription Duration for Gold Users
select Plan_type, avg(datediff(day,start_date,end_date)) as avg_duration
from subscriptions
where plan_type = 'Gold'
group by plan_type;

-- 18. Which Occupation Group Spends the Most Money?
select p.Occupation,
sum(case when plan_type = 'silver' then 999 
	when plan_type = 'gold' then 1999 else 0 end) as Total_spending
from profiles as p
join subscriptions as s on p.user_id = s.user_id
group by p.occupation
order by Total_spending desc;





-- 19. Is There Any Relationship Between Education Level and Subscription Plan?

select p.Education_level, s.Plan_type, Count(*) as user_count
from profiles as p
join subscriptions as s on p.user_id = s.user_id
group by p.education_level, s.plan_type
order by p.education_level, Count(*) desc;


-- 20. Top 5 city Where Marriage is the Most Popular Relationship Type
select top 5 city, count(*) as Marriage_count
from profiles
where looking_for = 'marriage'
Group by city
order by count(*) desc;

