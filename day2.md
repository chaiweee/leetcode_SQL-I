## 197. Rising Temperature  

Table: Weather  

| Column Name   | Type    |
| ------------- | ------- |
| id            | int     |
| recordDate    | date    |
| temperature   | int     |

id is the primary key for this table.  
This table contains information about the temperature on a certain day.  
Write an SQL query to find all dates' Id with higher temperatures compared to its previous dates (yesterday).  
Return the result table in any order.  
The query result format is in the following example.  
   
Example 1:  
Input:   
Weather table:  

| id | recordDate | temperature |
| -- | ---------- | ----------- |
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

Output:   

| id |
| -- |
| 2  |
| 4  |

A:  
```
select 
    b.id
from
    weather a join weather b
on
    datediff(b.recordDate, a.recordDate) = 1
where
    b.temperature > a.temperature;
```
*I did not know how to tackle this problem at all. Then I look at explanation online, it is easy if we know the purpose of self join. This question requires us to self join the table on the different dates using function DATEDIFF().
When I self join the table on DATEDIFF()=1, I get:*
```
| id | recordDate | temperature | id | recordDate | temperature |
| -- | ---------- | ----------- | -- | ---------- | ----------- |
| 1  | 2015-01-01 | 10          | 2  | 2015-01-02 | 25          |
| 2  | 2015-01-02 | 25          | 3  | 2015-01-03 | 20          |
| 3  | 2015-01-03 | 20          | 4  | 2015-01-04 | 30          |
```
*Then from the joined table I try to get the tempearture from b that is higher than a.*  

</br>

## 607. Sales Person  

Table: SalesPerson, Company, Orders  
| Column Name     | Type    |
| --------------- | ------- |
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |

sales_id is the primary key column for this table.  
Each row of this table indicates the name and the ID of a salesperson alongside their salary, commission rate, and hire date.  

| Column Name | Type    |
| ----------- | ------- |
| com_id      | int     |
| name        | varchar |
| city        | varchar |

com_id is the primary key column for this table.  
Each row of this table indicates the name and the ID of a company and the city in which the company is located.  

| Column Name | Type |
| ----------- | ---- |
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |

order_id is the primary key column for this table.  
com_id is a foreign key to com_id from the Company table.  
sales_id is a foreign key to sales_id from the SalesPerson table.  
Each row of this table contains information about one order. This includes the ID of the company, the ID of the salesperson, the date of the order, and the amount paid.  
Write an SQL query to report the names of all the salespersons who did not have any orders related to the company with the name "RED".  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
SalesPerson table:  

| sales_id | name | salary | commission_rate | hire_date  |
| -------- | ---- | ------ | --------------- | ---------- |
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |

Company table:  

| com_id | name   | city     |
| ------ | ------ | -------- |
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |

Orders table:  

| order_id | order_date | com_id | sales_id | amount |
| -------- | ---------- | ------ | -------- | ------ |
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |

Output:   

| name |
| ---- |
| Amy  |
| Mark |
| Alex |


A:  
```
select
    name
from
    salesperson where name not in 
	(
	select
	    b.name
	from
	    orders a 
	join salesperson b 
	on a.sales_id = b.sales_id
	join company c
	on a.com_id = c.com_id
	where c.name = 'RED');
```

*first find out who has sales on the company 'RED' then find out those who is not in the list*

</br>

## 1141. User Activity for the Past 30 Days I  

Table: Activity  

| Column Name   | Type    |
| ------------- | ------- |
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |

There is no primary key for this table, it may have duplicate rows.  
The activity_type column is an ENUM of type ('open_session', 'end_session', 'scroll_down', 'send_message').  
The table shows the user activities for a social media website.   
Note that each session belongs to exactly one user.  
Write an SQL query to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively.  
A user was active on someday if they made at least one activity on that day.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Activity table:  

| user_id | session_id | activity_date | activity_type |
| ------- | ---------- | ------------- | ------------- |
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |

Output:   
 
| day        | active_users |
| ---------- | ------------ | 
| 2019-07-20 | 2            |
| 2019-07-21 | 2            |


A:  
```
select
    activity_date as day,
    count(distinct user_id) as active_users
from
    activity
where
    activity_date between ('2019-07-27' - INTERVAL 29 DAY) and '2019-07-27'
group by
    activity_date;
```
*This question is to count daily active user so use COUNT DISTINCT and then GROUP BY should be ok. But the tricky part is the date, because it is 30 days until 2019-07-27 inclusively, 2019-07-27 should be counted as 1 day, hence we **only get the 29 days** before this date.*

</br>


## 1693. Daily Leads and Partners  

Table: DailySales  

| Column Name | Type    |
| ----------- | ------- |
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |

This table does not have a primary key.  
This table contains the date and the name of the product sold and the IDs of the lead and partner it was sold to.  
The name consists of only lowercase English letters.  
Write an SQL query that will, for each date_id and make_name, return the number of distinct lead_id's and distinct partner_id's.  
Return the result table in any order.  
The query result format is in the following example.  
  
Example 1:  
Input:   
DailySales table:  

| date_id   | make_name | lead_id | partner_id |
| --------- | ------- | ------------- | ------- |
| 2020-12-8 | toyota    | 0       | 1          |
| 2020-12-8 | toyota    | 1       | 0          |
| 2020-12-8 | toyota    | 1       | 2          |
| 2020-12-7 | toyota    | 0       | 2          |
| 2020-12-7 | toyota    | 0       | 1          |
| 2020-12-8 | honda     | 1       | 2          |
| 2020-12-8 | honda     | 2       | 1          |
| 2020-12-7 | honda     | 0       | 1          |
| 2020-12-7 | honda     | 1       | 2          |
| 2020-12-7 | honda     | 2       | 1          |

Output:   

| date_id   | make_name | unique_leads | unique_partners |
| ------------- | ------- | ------------- | ------- |
| 2020-12-8 | toyota    | 2            | 3               |
| 2020-12-7 | toyota    | 1            | 2               |
| 2020-12-8 | honda     | 2            | 2               |
| 2020-12-7 | honda     | 3            | 2               |


A:  
```
select
    date_id,
    make_name,
    count(distinct lead_id) as unique_leads,
    count(distinct partner_id) as unique_partners
from
    DailySales
group by
    date_id, make_name;
```

</br> 


## 1729. Find Followers Count    

Table: Followers   

| Column Name | Type |
| ----------- | ------- |
| user_id     | int  |
| follower_id | int  |

(user_id, follower_id) is the primary key for this table.  
This table contains the IDs of a user and a follower in a social media app where the follower follows the user.  

Write an SQL query that will, for each user, return the number of followers.  
Return the result table ordered by user_id in ascending order.  
The query result format is in the following example.  
   
Example 1:  
Input:   
Followers table:  

| user_id | follower_id |
| ----------- | ------- |
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |

Output:   

| user_id | followers_count|
| ----------- | ------- |
| 0       | 1              |
| 1       | 1              |
| 2       | 2              |

A:  
```
select
    user_id,
    count(follower_id) as followers_count
from
    followers
group by
    user_id
order by
    user_id;
```

</br>

## 586. Customer Placing the Largest Number of Orders  

Table: Orders  

| Column Name     | Type     |
| ----------- | ------- |
| order_number    | int      |
| customer_number | int      |

order_number is the primary key for this table.  
This table contains information about the order ID and the customer ID.  
 
Write an SQL query to find the customer_number for the customer who has placed the largest number of orders.  
The test cases are generated so that exactly one customer will have placed more orders than any other customer.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Orders table:  

| order_number | customer_number |
| ----------- | ------- |
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |

Output:   

| customer_number |
| ----------- |
| 3               |

A:  
```
select
    customer_number
from
    (
        select
            count(order_number) as cnt,
            customer_number
        from orders
        group by customer_number
        order by cnt desc
    ) aa
limit 1;
```

*I first find the count of orders for each customer then ORDER them from largest to smallest. Then only I can get the customer id for the largest one using LIMIT 1.*


</br>

## 511. Game Play Analysis I  

Table: Activity  

| Column Name  | Type    |
| ----------- | ------- |
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |

(player_id, event_date) is the primary key of this table.  
This table shows the activity of players of some games.  
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.  
Write an SQL query to report the first login date for each player.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Activity table:  

| player_id | device_id | event_date | games_played |
| ----------- | ------- | ----------- | ------- |
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |

Output:  
| player_id | first_login |
| ----------- | ------- |
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |

A:  
```
select
    player_id,
    min(event_date) as first_login
from
    Activity
group by
    player_id;
```

</br>

## 1890. The Latest Login in 2020  

Table: Logins  

| Column Name    | Type     |
| ----------- | ------- |
| user_id        | int      |
| time_stamp     | datetime |

(user_id, time_stamp) is the primary key for this table.  
Each row contains information about the login time for the user with ID user_id.  
Write an SQL query to report the latest login for all users in the year 2020. Do not include the users who did not login in 2020.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Logins table:  

| user_id | time_stamp          |
| ----------- | ------- |
| 6       | 2020-06-30 15:06:07 |
| 6       | 2021-04-21 14:06:06 |
| 6       | 2019-03-07 00:18:15 |
| 8       | 2020-02-01 05:10:53 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
| 2       | 2019-08-25 07:59:08 |
| 14      | 2019-07-14 09:00:00 |
| 14      | 2021-01-06 11:59:59 |

Output:  

| user_id | last_stamp          |
| ----------- | ------- |
| 6       | 2020-06-30 15:06:07 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |

A:  
```
select
    user_id,
    max(time_stamp) as last_stamp
from
    logins
where
    substr(time_stamp, 1, 4) = '2020'
group by user_id;
```
*This answer is accepted but it beats at only 11%. One way to improve runtime is instead of using SUBSTR, use YEAR(time_stamp) = '2020'*
</br>

## 1741. Find Total Time Spent by Each Employee  

Table: Employees   

| Column Name | Type |
| ----------- | ------- |
| emp_id      | int  |
| event_day   | date |
| in_time     | int  |
| out_time    | int  |

(emp_id, event_day, in_time) is the primary key of this table.  
The table shows the employees' entries and exits in an office.  
event_day is the day at which this event happened, in_time is the minute at which the employee entered the office, 
and out_time is the minute at which they left the office.  
in_time and out_time are between 1 and 1440.  
It is guaranteed that no two events on the same day intersect in time, and in_time < out_time.  
Write an SQL query to calculate the total time in minutes spent by each employee on each day at the office.  
Note that within one day, an employee can enter and leave more than once. The time spent in the office for a single entry is out_time - in_time.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employees table:  
| emp_id | event_day  | in_time | out_time |
| ----------- | ------- | ----------- | ------- |
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |

Output:   

| day        | emp_id | total_time |
| ----------- | ------- | ------ |
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |


A:    
```
select
    event_day as day,
    emp_id,
    sum(out_time - in_time) as total_time
from
    employees
group by
    event_day, emp_id;
```
</br>


## 1393. Capital Gain/Loss  (Medium)  

Table: Stocks    

| Column Name   | Type    |
| ----------- | ------- |
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |

(stock_name, operation_day) is the primary key for this table.  
The operation column is an ENUM of type ('Sell', 'Buy'). 
Each row of this table indicates that the stock which has stock_name had an operation on the day operation_day with the price.  
It is guaranteed that each 'Sell' operation for a stock has a corresponding 'Buy' operation in a previous day.  
It is also guaranteed that each 'Buy' operation for a stock has a corresponding 'Sell' operation in an upcoming day.  
Write an SQL query to report the Capital gain/loss for each stock.  
The Capital gain/loss of a stock is the total gain or loss after buying and selling the stock one or many times.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Stocks table:  
| stock_name    | operation | operation_day | price  |
| ----------- | ------- | ----------- | ------- |
| Leetcode      | Buy       | 1             | 1000   |
| Corona Masks  | Buy       | 2             | 10     |
| Leetcode      | Sell      | 5             | 9000   |
| Handbags      | Buy       | 17            | 30000  |
| Corona Masks  | Sell      | 3             | 1010   |
| Corona Masks  | Buy       | 4             | 1000   |
| Corona Masks  | Sell      | 5             | 500    |
| Corona Masks  | Buy       | 6             | 1000   |
| Handbags      | Sell      | 29            | 7000   |
| Corona Masks  | Sell      | 10            | 10000  |

Output:  
| stock_name    | capital_gain_loss |
| ----------- | ------- |
| Corona Masks  | 9500              |
| Leetcode      | 8000              |
| Handbags      | -23000            |

A:  
```
select
    stock_name,
    sum(capital) as capital_gain_loss
from
(
    select
        stock_name,
        case
            when operation = 'Buy' then -price
            else price
        end as capital
    from
        stocks
)aa
group by
    stock_name;
```
*I didn’t know how to tackle this problem at first, I also thought maybe self join and comparing the sell and buy for each day would give me the answer, obviously I was overthinking it. The question wants the total gain loss for each stock, hence just by giving negative to buy and positive to sell and then add them all up would give me the answer. *

