## 	619. Biggest Single Number  

Table: MyNumbers  

| Column Name | Type |
| ------------ | ---- |
| num         | int  |

There is no primary key for this table. It may contain duplicates.  
Each row of this table contains an integer.  
A single number is a number that appeared only once in the MyNumbers table.  
Write an SQL query to report the largest single number. If there is no single number, report null.  
The query result format is in the following example.  

Example 1:  
Input:   
MyNumbers table:  

| num |
| ---- |
| 8   |
| 8   |
| 3   |
| 3   |
| 1   |
| 4   |
| 5   |
| 6   |

Output:   

| num |
| ---- |
| 6   |

A:  
```
select
    max(num) as num
from
(
    select
        num,
        case
            when count(num)=1 then 1
            else 0
        end as cnt
    from
        MyNumbers
    group by num
)aa where cnt = 1;
```
*First get the count of number appears, then get the max number*  


</br>

## 	620. Not Boring Movies  

Table: Cinema  
| Column Name    | Type     |
| ------------ | ---- |
| id             | int      |
| movie          | varchar  |
| description    | varchar  |
| rating         | float    |

id is the primary key for this table.  
Each row contains information about the name of a movie, its genre, and its rating.  
rating is a 2 decimal places float in the range [0, 10]. 
Write an SQL query to report the movies with an odd-numbered ID and a description that is not "boring".  
Return the result table ordered by rating in descending order.  
The query result format is in the following example.  

Example 1:  
Input:   
Cinema table:  

| id | movie      | description | rating |
| ----- | ---- | ------------ | ---- |
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |

Output: 

| id | movie      | description | rating |
| ------------ | ---- | ------------ | ---- |
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |

A:  
```
select
    *
from
    cinema
where
    id in (select id from cinema where id%2!=0)
    and description not like '%boring%'
order by
    rating desc;
```
*To get odd number id, use modulo%2!=0. I checked the solution of other people and realise there is a function for modulo. The answer below has lower runtime.*  

```
SELECT * from Cinema WHERE MOD(id,2)=1  AND description!='boring' order by rating desc;
```


</br>



## 626. Exchange Seats (Medium)  

Table: Seat  

| Column Name | Type    |
| ------------ | ---- |
| id          | int     |
| student     | varchar |

id is the primary key column for this table.  
Each row of this table indicates the name and the ID of a student.  
id is a continuous increment.  
Write an SQL query to swap the seat id of every two consecutive students. If the number of students is odd, the id of the last student is not swapped.  
Return the result table ordered by id in ascending order.  
The query result format is in the following example.  

Example 1:  
Input:   
Seat table:  

| id | student |
| ------------ | ---- |
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |

Output:   

| id | student |
| ------------ | ---- |
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |

A:  
```
select
    id,
    case
        when mod(id,2) = 0 then lag(student) over (order by id) # even
        else ifnull(lead(student) over (order by id), student)
    end as student 
from
    seat
order by id;
```
*This question is not hard if we know the concept of LAG() and LEAD() like in Question 180. Consecutive Numbers. Beware when it says if the last id is odd number then we do not have to swap, that is why we used the IFNULL()*

</br>

## 	1045. Customers who bought all products (Medium)  

Table: Customer, Product  

| Column Name | Type    |
| ------------ | ---- |
| customer_id | int     |
| product_key | int     |

There is no primary key for this table. It may contain duplicates. customer_id is not NULL.  
product_key is a foreign key to Product table.  

| Column Name | Type    |
| ------------ | ---- |
| product_key | int     |

product_key is the primary key column for this table.  
Write an SQL query to report the customer ids from the Customer table that bought all the products in the Product table.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Customer table:  

| customer_id | product_key |
| ------------ | ---- |
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |

Product table:  

| product_key |
| ------------ | 
| 5           |
| 6           |

Output:   

| customer_id |
| ------------ |
| 1           |
| 3           |

A:  
```
select
    customer_id
from
    customer cust
group by
    customer_id
having
    count(distinct cust.product_key) = (select count(distinct product_key) from product)
```
*At first I thought of comparing all the product each customer buy and all the product in the product table. But that didn’t specifically show all products. Then I refer to the solutions only to realize I have to compare the number of distinct product bought by customer with the number of distinct product in Product table.*  
*My solution has high runtime hence I found another solution that has lower runtime.*  
```
With tb as 
(SELECT DISTINCT customer_id, count(DISTINCT product_key) num from Customer
group by customer_id)
Select customer_id from tb
where num = (Select count(product_key) from Product)
```

</br>

## 1068. Product Sales Analysis I  

Table: Sales, Product  

| Column Name | Type  |
| ------------ | ---- |
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |

(sale_id, year) is the primary key of this table.  
product_id is a foreign key to Product table.   
Each row of this table shows a sale on the product product_id in a certain year.  
Note that the price is per unit.  

| Column Name  | Type    |
| ------------ | ---- |
| product_id   | int     |
| product_name | varchar |

product_id is the primary key of this table.  
Each row of this table indicates the product name of each product.  
Write an SQL query that reports the product_name, year, and price for each sale_id in the Sales table.  
Return the resulting table in any order.  
The query result format is in the following example.  
  
Example 1:  
Input:   
Sales table:  

| sale_id | product_id | year | quantity | price |
| ------------ | ---- | ------------ | ---- | ---- | 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |

Product table:  

| product_id | product_name |
| ------------ | ---- |
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |

Output:   

| product_name | year  | price |
| ------------ | ---- | ---- |
| Nokia        | 2008  | 5000  |
| Nokia        | 2009  | 5000  |
| Apple        | 2011  | 9000  |

A:    
```
select
    b.product_name,
    a.year,
    a.price
from sales a
left join product b
on a.product_id = b.product_id;
```
</br>


## 	184. Department Highest Salary (Medium)  

Table: Employee, Department  

| Column Name  | Type    |
| ------------ | ---- |
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

id is the primary key column for this table.  
departmentId is a foreign key of the ID from the Department table.  
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.  

| Column Name | Type    |
| ------------ | ---- |
| id          | int     |
| name        | varchar |
  
id is the primary key column for this table. It is guaranteed that department name is not NULL.  
Each row of this table indicates the ID of a department and its name.  
Write an SQL query to find employees who have the highest salary in each of the departments.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employee table:  

| id | name  | salary | departmentId |
| ------------ | ---- | ------------ | ---- |
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |

Department table:  

| id | name  |
| ------------ | ---- |
| 1  | IT    |
| 2  | Sales |

Output:   

| Department | Employee | Salary |
| ------------ | ---- | ---- |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |

A:  
```
select
    aa.Department,
    c.name as Employee,
    aa.Salary
from
(
    select
        b.name as Department,
        b.id,
        max(salary) as Salary
    from
        employee a
    left join
        department b
    on a.departmentId = b.id
    group by b.name
)aa left join employee c
on aa.id = c.departmentid and aa.salary = c.salary;
```
*This question was not as easy as I thought but give it abit more thoughts and you can get the answer.*
*First getting the highest salary from each department, and then get the employee name from that department and with that salary.*   

</br>

## 	185. Department Top Three Salaries (Hard)  

Table: Employee, Department  

| Column Name  | Type    |
| ------------ | ---- |
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

id is the primary key column for this table.  
departmentId is a foreign key of the ID from the Department table.  
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.  

| Column Name | Type    |
| ------------ | ---- |
| id          | int     |
| name        | varchar |

id is the primary key column for this table.  
Each row of this table indicates the ID of a department and its name.  

A company's executives are interested in seeing who earns the most money in each of the company's departments.  
A high earner in a department is an employee who has a salary in the top three unique salaries for that department.  
Write an SQL query to find the employees who are high earners in each of the departments.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employee table:  

| id | name  | salary | departmentId |
| ------------ | ---- | ------------ | ---- |
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

Department table:  

| id | name  |
| ------------ | ---- |
| 1  | IT    |
| 2  | Sales |
 
Output:   

| Department | Employee | Salary |
| ------------ | ---- | ---- |
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

A:  
```
select
    department,
    employee,
    salary
from
(
    SELECT
        b.name as department,
        a.name as employee,
        DENSE_RANK() OVER (PARTITION BY departmentid ORDER BY salary DESC) AS 'rank',
        salary
    FROM employee a 
    left join department b
    on a.departmentid = b.id
    group by b.name, a.name
)aa
where aa.rank<4;
```
*This question is a continued question from the last one. I knew I had to use DENSE_RANK() function with this one but I did not know how to use PARTITION BY at first. After referencing to others solution, I found out that I have to use PARTITION BY in DENSE_RANK().*

</br>

## 	1204. Last Person to Fit in the Bus (Medium)  

Table: Queue  

| Column Name | Type    |
| ------------ | ---- |
| person_id   | int     |
| person_name | varchar |
| weight      | int     |
| turn        | int     |

person_id is the primary key column for this table.  
This table has the information about all people waiting for a bus.  
The person_id and turn columns will contain all numbers from 1 to n, where n is the number of rows in the table.  
turn determines the order of which the people will board the bus, where turn=1 denotes the first person to board and turn=n denotes the last person to board.  
weight is the weight of the person in kilograms.  
There is a queue of people waiting to board a bus. However, the bus has a weight limit of 1000 kilograms, so there may be some people who cannot board.  
Write an SQL query to find the person_name of the last person that can fit on the bus without exceeding the weight limit. The test cases are generated such that the first person does not exceed the weight limit.  
The query result format is in the following example.  

Example 1:  
Input:   
Queue table:  

| person_id | person_name | weight | turn |
| ------------ | ---- | ------------ | ---- |
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |

Output:   

| person_name |
| ------------ |
| John Cena   |

Explanation: The folowing table is ordered by the turn for simplicity.  

| Turn | ID | Name      | Weight | Total Weight |
| ------------ | ---- | ------------ | ---- | ---- |
| 1    | 5  | Alice     | 250    | 250          |
| 2    | 3  | Alex      | 350    | 600          |
| 3    | 6  | John Cena | 400    | 1000         | 
| 4    | 2  | Marie     | 200    | 1200         | 
| 5    | 4  | Bob       | 175    | ___          |
| 6    | 1  | Winston   | 500    | ___          |  

John is (last person to board)  
Marie (cannot board)  

A:  
```
with aa as(
select
    person_name,
    sum(weight) over (order by turn) as total
from
    queue)
    
select
    person_name
from
    aa
where tmp <=1000
order by total desc
limit 1;
```
*This question is hard :( To solve this you have to know SUM() OVER (ORDER BY ) function so that you can sum the weight according to their turn. Then ORDER BY the total weight DESC and get the one <= 1000*

</br>

## 	570. Managers with at least 5 Direct Reports  

Table: Employee  
| Column Name | Type    |
| ------------ | ---- |
| id          | int     |
| name        | varchar |
| department  | varchar |
| managerId   | int     |

id is the primary key column for this table.  
Each row of this table indicates the name of an employee, their department, and the id of their manager.  
If managerId is null, then the employee does not have a manager.  
No employee will be the manager of themself.  
Write an SQL query to report the managers with at least five direct reports.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employee table:  

| id  | name  | department | managerId |
| ------------ | ---- | ------------ | ---- |
| 101 | John  | A          | None      |
| 102 | Dan   | A          | 101       |
| 103 | James | A          | 101       |
| 104 | Amy   | A          | 101       |
| 105 | Anne  | A          | 101       |
| 106 | Ron   | B          | 101       |

Output:   

| name |
| ------------ |
| John |

A:  
```
with tmp as(
select
    managerId,
    count(managerId) as cnt
from
    employee
group by managerId)
select
    a.name
from
    tmp
join employee a
on tmp.managerId = a.id
where cnt >=5;
```
*At first I thought just by adding HAVING clause with the COUNT(managerId) >4 could solve but it failed at testcase 3. Turned out we have to first get the count of the managerId first then only get the name. Another simpler solution is as below:*  
```
select name from employee 
where id in 
(select managerId from Employee
group by managerId
having count(managerId)>=5) 
```

</br>

## 	262. Trips and Users (Hard)  

Table: Trips, Users  

| Column Name | Type     |
| ------------ | ---- |
| id          | int      |
| client_id   | int      |
| driver_id   | int      |
| city_id     | int      |
| status      | enum     |
| request_at  | date     |     

id is the primary key for this table.  
The table holds all taxi trips. Each trip has a unique id, while client_id and driver_id are foreign keys to the users_id at the Users table.  
Status is an ENUM type of ('completed', 'cancelled_by_driver', 'cancelled_by_client').  

| Column Name | Type     |
| ------------ | ---- |
| users_id    | int      |
| banned      | enum     |
| role        | enum     |

users_id is the primary key for this table.  
The table holds all users. Each user has a unique users_id, and role is an ENUM type of ('client', 'driver', 'partner').  
banned is an ENUM type of ('Yes', 'No').  
The cancellation rate is computed by dividing the number of canceled (by client or driver) requests with unbanned users by the total number of requests with unbanned users on that day.  
Write a SQL query to find the cancellation rate of requests with unbanned users (both client and driver must not be banned) each day between "2013-10-01" and "2013-10-03". Round Cancellation Rate to two decimal points.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Trips table:  

| id | client_id | driver_id | city_id | status              | request_at |
| ------------ | ---- | ------------ | ---- | ------------ | ---- |
| 1  | 1         | 10        | 1       | completed           | 2013-10-01 |
| 2  | 2         | 11        | 1       | cancelled_by_driver | 2013-10-01 |
| 3  | 3         | 12        | 6       | completed           | 2013-10-01 |
| 4  | 4         | 13        | 6       | cancelled_by_client | 2013-10-01 |
| 5  | 1         | 10        | 1       | completed           | 2013-10-02 |
| 6  | 2         | 11        | 6       | completed           | 2013-10-02 |
| 7  | 3         | 12        | 6       | completed           | 2013-10-02 |
| 8  | 2         | 12        | 12      | completed           | 2013-10-03 |
| 9  | 3         | 10        | 12      | completed           | 2013-10-03 |
| 10 | 4         | 13        | 12      | cancelled_by_driver | 2013-10-03 |

Users table:  

| users_id | banned | role   |
| ------------ | ------------ | ---- |
| 1        | No     | client |
| 2        | Yes    | client |
| 3        | No     | client |
| 4        | No     | client |
| 10       | No     | driver |
| 11       | No     | driver |
| 12       | No     | driver |
| 13       | No     | driver |

Output:   

| Day        | Cancellation Rate |
| ------------ | ---- |
| 2013-10-01 | 0.33              |
| 2013-10-02 | 0.00              |
| 2013-10-03 | 0.50              |

Explanation:   
On 2013-10-01:  
  - There were 4 requests in total, 2 of which were canceled.  
  - However, the request with Id=2 was made by a banned client (User_Id=2), so it is ignored in the calculation.  
  - Hence there are 3 unbanned requests in total, 1 of which was canceled.  
  - The Cancellation Rate is (1 / 3) = 0.33  

On 2013-10-02:  
  - There were 3 requests in total, 0 of which were canceled.  
  - The request with Id=6 was made by a banned client, so it is ignored.  
  - Hence there are 2 unbanned requests in total, 0 of which were canceled.  
  - The Cancellation Rate is (0 / 2) = 0.00  

On 2013-10-03:  
  - There were 3 requests in total, 1 of which was canceled.  
  - The request with Id=8 was made by a banned client, so it is ignored.  
  - Hence there are 2 unbanned request in total, 1 of which were canceled.  
  - The Cancellation Rate is (1 / 2) = 0.50  

A:  
```
select
    request_at as Day,
    round( ( sum(case when status like '%cancelled%' then 1 else 0 end) / count(status) ) ,2) as 'Cancellation Rate'
from
    trips a
where
    a.client_id not in (select users_id from users where banned = 'Yes')
    and a.driver_id not in (select users_id from users where banned = 'Yes')
    and request_at between '2013-10-01' and '2013-10-03'
group by request_at;
```
*This is a hard one :( I did not think about using SUM(CASE WHEN) in this case. When you see that you can use SUM(CASE WHEN) to do the cancellation rate then the rest will not be a problem. Beware of the date the question was asking cause mine failed at testcase 11 because I did not add date condition.*




