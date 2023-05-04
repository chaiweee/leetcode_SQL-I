## 	1683. Invalid Tweets

Table: Tweets  

| Column Name    | Type    |
| ------------ | ---- |
| tweet_id       | int     |
| content        | varchar |  

tweet_id is the primary key for this table.  
This table contains all the tweets in a social media app.  

Write an SQL query to find the IDs of the invalid tweets.  
The tweet is invalid if the number of characters used in the content of the tweet is strictly greater than 15.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Tweets table:  

| tweet_id | content                          |
| ------------ | ---- |
| 1        | Vote for Biden                   |
| 2        | Let us make America great again! |

Output:   

| tweet_id |
| ------------- |
| 2        |

Explanation:   
Tweet 1 has length = 14. It is a valid tweet.  
Tweet 2 has length = 32. It is an invalid tweet.  

A:  
```
select
    tweet_id
from
    tweets
where
    length(content) > 15;
```

</br>


## 1378. Replace Employee ID With The Unique Identifier

Table: Employees, EmployeeUNI  

| Column Name   | Type    |
| ------------ | ---- |
| id            | int     |
| name          | varchar |

id is the primary key for this table.  
Each row of this table contains the id and the name of an employee in a company.  

| Column Name   | Type    |
| ------------ | ---- |
| id            | int     |
| unique_id     | int     |

(id, unique_id) is the primary key for this table.  
Each row of this table contains the id and the corresponding unique id of an employee in the company.  
Write an SQL query to show the unique ID of each user, If a user does not have a unique ID replace just show null.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employees table:  
| id | name     |
| ------------ | ---- |
| 1  | Alice    |
| 7  | Bob      |
| 11 | Meir     |
| 90 | Winston  |
| 3  | Jonathan |

EmployeeUNI table:  

| id | unique_id |
| ------------ | ---- |
| 3  | 1         |
| 11 | 2         |
| 90 | 3         |

Output:   

| unique_id | name     |
| ------------ | ---- |
| null      | Alice    |
| null      | Bob      |
| 2         | Meir     |
| 3         | Winston  |
| 1         | Jonathan |

Explanation:   
Alice and Bob do not have a unique ID, We will show null instead.  
The unique ID of Meir is 2.  
The unique ID of Winston is 3.  
The unique ID of Jonathan is 1.  

A:    
```
select
    unique_id,
    name
from
    employees a
left join
    employeeuni b
on
    a.id = b.id;
```

</br>


## 1661. Average Time of Process per Machine  

Table: Activity  

| Column Name    | Type    |
| ------------ | ---- |
| machine_id     | int     |
| process_id     | int     |
| activity_type  | enum    |
| timestamp      | float   |

The table shows the user activities for a factory website.  
(machine_id, process_id, activity_type) is the primary key of this table.  
machine_id is the ID of a machine.  
process_id is the ID of a process running on the machine with ID machine_id.  
activity_type is an ENUM of type ('start', 'end').  
timestamp is a float representing the current time in seconds.  
'start' means the machine starts the process at the given timestamp and 'end' means the machine ends the process at the given timestamp.  
The 'start' timestamp will always be before the 'end' timestamp for every (machine_id, process_id) pair.  

There is a factory website that has several machines each running the same number of processes. Write an SQL query to find the average time each machine takes to complete a process.  
The time to complete a process is the 'end' timestamp minus the 'start' timestamp. The average time is calculated by the total time to complete every process on the machine divided by the number of processes that were run.  
The resulting table should have the machine_id along with the average time as processing_time, which should be rounded to 3 decimal places.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Activity table:  

| machine_id | process_id | activity_type | timestamp |
| ------------ | ---- | ------------ | ---- |
| 0          | 0          | start         | 0.712     |
| 0          | 0          | end           | 1.520     |
| 0          | 1          | start         | 3.140     |
| 0          | 1          | end           | 4.120     |
| 1          | 0          | start         | 0.550     |
| 1          | 0          | end           | 1.550     |
| 1          | 1          | start         | 0.430     |
| 1          | 1          | end           | 1.420     |
| 2          | 0          | start         | 4.100     |
| 2          | 0          | end           | 4.512     |
| 2          | 1          | start         | 2.500     |
| 2          | 1          | end           | 5.000     |

Output:   

| machine_id | processing_time |
| ------------ | ---- |
| 0          | 0.894           |
| 1          | 0.995           |
| 2          | 1.456           |

Explanation:   
There are 3 machines running 2 processes each.  
Machine 0's average time is ((1.520 - 0.712) + (4.120 - 3.140)) / 2 = 0.894  
Machine 1's average time is ((1.550 - 0.550) + (1.420 - 0.430)) / 2 = 0.995  
Machine 2's average time is ((4.512 - 4.100) + (5.000 - 2.500)) / 2 = 1.456  

A:   
```
select
    machine_id,
    round(sum(processing_time) /count(distinct process_id), 3) as processing_time
from
(
    select
        machine_id,
        process_id,
        case
            when activity_type = 'start' then -timestamp
            else timestamp
        end as processing_time
    from
        activity
) aa
group by
    machine_id;

```
*This is a question that requires abit more thinking. I did not know how to tackle at first but then I remember I have encountered this type of question before which was question 1393(Day2). To find the timestamp difference between activity type start and end, just give the time to be minus a (-) and then sum them up. To get average just divide it by the count of process id.*


</br>

## 577. Employee Bonus  

Table: Employee, Bonus  

| Column Name | Type    |
| ------------ | ---- |
| empId       | int     |
| name        | varchar |
| supervisor  | int     |
| salary      | int     |
  
empId is the primary key column for this table.  
Each row of this table indicates the name and the ID of an employee in addition to their salary and the id of their manager.  

| Column Name | Type |
| ------------ | ---- |
| empId       | int  |
| bonus       | int  |

empId is the primary key column for this table.  
empId is a foreign key to empId from the Employee table.  
Each row of this table contains the id of an employee and their respective bonus.  

Write an SQL query to report the name and bonus amount of each employee with a bonus less than 1000.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Employee table:  

| empId | name   | supervisor | salary |
| ------------ | ---- | ------------ | ---- |
| 3     | Brad   | null       | 4000   |
| 1     | John   | 3          | 1000   |
| 2     | Dan    | 3          | 2000   |
| 4     | Thomas | 3          | 4000   |

Bonus table:  

| empId | bonus |
| ------------ | ---- |
| 2     | 500   |
| 4     | 2000  |

Output:   

| name | bonus |
| --- | ---- |
| Brad | null  |
| John | null  |
| Dan  | 500   |


A:  
```
select
    name,
    bonus
from
    employee a
left join
    bonus b
on
    a.empid = b.empid
where
    b.bonus < 1000 or b.bonus is null;

```

</br>

## 1280. Students and Examinations  

Table: Students, Subjects, Examinations  
 
| Column Name   | Type    |
| ------------ | ---- |
| student_id    | int     |
| student_name  | varchar |

student_id is the primary key for this table.  
Each row of this table contains the ID and the name of one student in the school.  

| Column Name  | Type    |
| ------------ | ---- |
| subject_name | varchar |

subject_name is the primary key for this table.  
Each row of this table contains the name of one subject in the school.  

| Column Name  | Type    |
| ------------ | ---- |
| student_id   | int     |
| subject_name | varchar |

There is no primary key for this table. It may contain duplicates.  
Each student from the Students table takes every course from the Subjects table.  
Each row of this table indicates that a student with ID student_id attended the exam of subject_name.  

Write an SQL query to find the number of times each student attended each exam.  
Return the result table ordered by student_id and subject_name.  
The query result format is in the following example.  

Example 1:  
Input:   
Students table:  

| student_id | student_name |
| ------------ | ---- |
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |

Subjects table:  

| subject_name |
| ----------- |
| Math         |
| Physics      |
| Programming  |

Examinations table:  

| student_id | subject_name |
| ------------ | ---- |
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |

Output:   

| student_id | student_name | subject_name | attended_exams |
| ------------ | ---- | ------------ | ---- |
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |

Explanation:   
The result table should contain all students and all subjects.  
Alice attended the Math exam 3 times, the Physics exam 2 times, and the Programming exam 1 time.  
Bob attended the Math exam 1 time, the Programming exam 1 time, and did not attend the Physics exam.  
Alex did not attend any exams.  
John attended the Math exam 1 time, the Physics exam 1 time, and the Programming exam 1 time.  

A:   
```
select
    aa.*,
    count(b.subject_name) as attended_exams
from
(
    select
        *
    from
        students a 
    join
        subjects b
)aa
left join
    examinations b
on
    aa.student_id = b.student_id
    and aa.subject_name = b.subject_name
group by
    aa.student_id, aa.subject_name
order by
    aa.student_id, aa.subject_name;
```
*At first I attempted another SQL which keeps failed at testcase 7, one of the student BOB always got null for his subject. That was because I did not use the Subjects table Because I thought it was unnecessary. So how can we tackle this problem?*
*First we need to assign the subjects to the students by JOINing students to subjects. Then we LEFT JOIN the examinations they took using student id and subject names, finally count the number of subject name in the table.*


</br>

## 1934. Confirmation Rate (Medium)  

Table: Signups, Confirmations  

| Column Name    | Type     |
| ------------ | ---- |
| user_id        | int      |
| time_stamp     | datetime |

user_id is the primary key for this table.  
Each row contains information about the signup time for the user with ID user_id.  

| Column Name    | Type     |
| ------------ | ---- |
| user_id        | int      |
| time_stamp     | datetime |
| action         | ENUM     |

(user_id, time_stamp) is the primary key for this table.  
user_id is a foreign key with a reference to the Signups table.  
action is an ENUM of the type ('confirmed', 'timeout'). 
Each row of this table indicates that the user with ID user_id requested a confirmation message at time_stamp and that confirmation message was either confirmed ('confirmed') or expired without confirming ('timeout').  
The confirmation rate of a user is the number of 'confirmed' messages divided by the total number of requested confirmation messages. The confirmation rate of a user that did not request any confirmation messages is 0. Round the confirmation rate to two decimal places.  
Write an SQL query to find the confirmation rate of each user.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Signups table:  

| user_id | time_stamp          |
| ------------ | ---- |
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |

Confirmations table:  

| user_id | time_stamp          | action    |
| ------------ | ------------- | ---- |
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-07-14 14:00:00 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2021-06-14 13:59:27 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |
| 2       | 2021-02-28 23:59:59 | timeout   |

Output:   

| user_id | confirmation_rate |
| ------------ | ---- |
| 6       | 0.00              |
| 3       | 0.00              |
| 7       | 1.00              |
| 2       | 0.50              |

Explanation:   
User 6 did not request any confirmation messages. The confirmation rate is 0.  
User 3 made 2 requests and both timed out. The confirmation rate is 0.  
User 7 made 3 requests and all were confirmed. The confirmation rate is 1.  
User 2 made 2 requests where one was confirmed and the other timed out. The confirmation rate is 1 / 2 = 0.5.  

A:  
```
select
        a.user_id,
        case
            when b.time_stamp is null then 0
            else round(sum(if(b.action='confirmed', 1,0))/ count(*), 2)
        end as confirmation_rate
    from
        signups a
    left join
        confirmations b
    on
        a.user_id = b.user_id
    group by a.user_id
```
*This question is hard although I know the logic to it. We have to get the sum of confirmed requests divided by the sum of requests. It will be easy if we know the function SUM(IF()). Important function!!!*


</br>


## 1251. Average Selling Price

Table: Prices, UnitsSold  

| Column Name   | Type    |
| ------------ | ---- |
| product_id    | int     |
| start_date    | date    |
| end_date      | date    |
| price         | int     |

(product_id, start_date, end_date) is the primary key for this table.  
Each row of this table indicates the price of the product_id in the period from start_date to end_date.  
For each product_id there will be no two overlapping periods. That means there will be no two intersecting periods for the same product_id.  

| Column Name   | Type    |
| ------------ | ---- |
| product_id    | int     |
| purchase_date | date    |
| units         | int     |

There is no primary key for this table, it may contain duplicates.  
Each row of this table indicates the date, units, and product_id of each product sold.   
Write an SQL query to find the average selling price for each product. average_price should be rounded to 2 decimal places.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Prices table:  

| product_id | start_date | end_date   | price  |
| ------------ | ---- | ------------ | ---- |
| 1          | 2019-02-17 | 2019-02-28 | 5      |
| 1          | 2019-03-01 | 2019-03-22 | 20     |
| 2          | 2019-02-01 | 2019-02-20 | 15     |
| 2          | 2019-02-21 | 2019-03-31 | 30     |

UnitsSold table:  

| product_id | purchase_date | units |
| ------------ | ------------ | ---- |
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |

Output:   

| product_id | average_price |
| ------------ | ---- |
| 1          | 6.96          |
| 2          | 16.96         |

Explanation:   
Average selling price = Total Price of Product / Number of products sold.  
Average selling price for product 1 = ((100 * 5) + (15 * 20)) / 115 = 6.96  
Average selling price for product 2 = ((200 * 15) + (30 * 30)) / 230 = 16.96  

A:  
```
select
    a.product_id,
    round(sum(price*units) / sum(units),2) as average_price
from
    prices a
left join
    unitssold b
on
    a.product_id = b.product_id
    and b.purchase_date between a.start_date and a.end_date
group by
    a.product_id;
```
*At first I was confused with how to get the average selling price, I was thinking about using the function AVG(). It turned out we have to do like this: 
In order to get the average price, we have to JOIN the prices table and unitssold table ON product_id and the purchase_date because the prices are different. Then we have to get the total selling price by SUM(price*units). Then it is divided by the total number of units sold to get the average price for each product.*


</br>

## 1075. Project Employees I  

Table: Project, Employee  

| Column Name | Type    |
| ------------ | ---- |
| project_id  | int     |
| employee_id | int     |

(project_id, employee_id) is the primary key of this table.  
employee_id is a foreign key to Employee table.  
Each row of this table indicates that the employee with employee_id is working on the project with project_id.  

| Column Name      | Type    |
| ------------ | ---- |
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |

employee_id is the primary key of this table. It's guaranteed that experience_years is not NULL.  
Each row of this table contains information about one employee.  
Write an SQL query that reports the average experience years of all the employees for each project, rounded to 2 digits.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Project table:  

| project_id  | employee_id |
| ------------ | ---- |
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |

Employee table:  

| employee_id | name   | experience_years |
| ------------ | ------------- | ---- |
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |

Output:   

| project_id  | average_years |
| ------------ | ---- |
| 1           | 2.00          |
| 2           | 2.50          |

Explanation: The average experience years for the first project is (3 + 2 + 1) / 3 = 2.00 and for the second project is (3 + 2) / 2 = 2.50  

A:  
```
select
    a.project_id,
    round(sum(b.experience_years)/count(*),2) as average_years
from
    project a
left join
    employee b
on
    a.employee_id = b.employee_id
group by
    project_id;
```


</br>

## 1633. Percentage of Users Attended a Contest  

Table: Users, Register

| Column Name | Type    |
| ------------ | ---- |
| user_id     | int     |
| user_name   | varchar |

user_id is the primary key for this table.    
Each row of this table contains the name and the id of a user.  

| Column Name | Type    |
| ------------ | ---- |
| contest_id  | int     |
| user_id     | int     |

(contest_id, user_id) is the primary key for this table.  
Each row of this table contains the id of a user and the contest they registered into.  
Write an SQL query to find the percentage of the users registered in each contest rounded to two decimals.  
Return the result table ordered by percentage in descending order. In case of a tie, order it by contest_id in ascending order.  
The query result format is in the following example.  

Example 1:  
Input:   
Users table:  

| user_id | user_name |
| ------------ | ---- |
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |

Register table:  
| contest_id | user_id |
| ------------ | ---- |
| 215        | 6       |
| 209        | 2       |
| 208        | 2       |
| 210        | 6       |
| 208        | 6       |
| 209        | 7       |
| 209        | 6       |
| 215        | 7       |
| 208        | 7       |
| 210        | 2       |
| 207        | 2       |
| 210        | 7       |

Output:   

| contest_id | percentage |
| ------------ | ---- |
| 208        | 100.0      |
| 209        | 100.0      |
| 210        | 100.0      |
| 215        | 66.67      |
| 207        | 33.33      |

Explanation:   
All the users registered in contests 208, 209, and 210. The percentage is 100% and we sort them in the answer table by contest_id in ascending order.  
Alice and Alex registered in contest 215 and the percentage is ((2/3) * 100) = 66.67%  
Bob registered in contest 207 and the percentage is ((1/3) * 100) = 33.33%  

A:  
```
select
    a.contest_id,
    round((count(b.user_id) / (select count(*) from users)) * 100, 2) as percentage
from
    register a
left join
    users b
on
    a.user_id = b.user_id
group by contest_id
order by percentage desc, contest_id;
```


</br>

## 1211. Queries Quality and Percentage  

Table: Queries  

| Column Name | Type    |
| ------------ | ---- |
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |

There is no primary key for this table, it may have duplicate rows.  
This table contains information collected from some queries on a database.  
The position column has a value from 1 to 500.  
The rating column has a value from 1 to 5. Query with rating less than 3 is a poor query.  
We define query quality as:  
The average of the ratio between query rating and its position.  
We also define poor query percentage as:  
The percentage of all queries with rating less than 3.  
Write an SQL query to find each query_name, the quality and poor_query_percentage.  
Both quality and poor_query_percentage should be rounded to 2 decimal places.  
Return the result table in any order.  
The query result format is in the following example.  

Example 1:  
Input:   
Queries table:  

| query_name | result            | position | rating |
| ------------ | ---- | ------------ | ---- |
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |

Output:   

| query_name | quality | poor_query_percentage |
| ------------ | --------- | ---- |
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |

Explanation:   
Dog queries quality is ((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50  
Dog queries poor_ query_percentage is (1 / 3) * 100 = 33.33  
Cat queries quality equals ((2 / 5) + (3 / 3) + (4 / 7)) / 3 = 0.66  
Cat queries poor_ query_percentage is (1 / 3) * 100 = 33.33  

A:  
```
select
    query_name,
    round(sum(rating/position) / count(*),2) as quality,
    round((sum(if(rating < 3, 1, 0)) / count(*))*100,2) as poor_query_percentage
from
    queries
group by
    query_name;
```
*Once again, using the SUM(IF()) function. The logic is this: 
We first need to get the quality, which is the sum of ration of rating and position, divided by total number of count for each query_name. Then we have to figure out the poor query percentage, which is if the rating is < 3 then it is poor so we add them up and divided by total number of count for each query_name again, multiply by 100 to make it percentage.*
