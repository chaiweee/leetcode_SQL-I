## 1667. Fix Names in a Table  

Table: Users  
| Column Name    | Type    |
| -------------- | ------- |
| user_id        | int     |
| name           | varchar |

user_id is the primary key for this table.  
This table contains the ID and the name of the user. The name consists of only lowercase and uppercase characters.  

Write an SQL query to fix the names so that only the first character is uppercase and the rest are lowercase.  
Return the result table ordered by user_id.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Users table:  

| user_id | name  |
| ------- | ------|
| 1       | aLice |
| 2       | bOB   |

Output:   
| user_id | name  |
| ------- | ------|
| 1       | Alice |
| 2       | Bob   |


A:  
```
select
    user_id,
    concat(upper(substr(name,1,1)), lower(substr(name,2))) as name
from
    users
order by user_id;
```


</br>

## 1484. Group Sold Products By The Date

Table: Activities

| Column Name | Type    |
| ----------- | ------- |
| sell_date   | date    |
| product     | varchar |

There is no primary key for this table, it may contain duplicates.  
Each row of this table contains the product name and the date it was sold in a market.  

Write an SQL query to find for each date the number of different products sold and their names.  
The sold products names for each date should be sorted lexicographically.  
Return the result table ordered by sell_date.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Activities table:  

| sell_date  | product     |
| ---------- | ----------- |
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |

Output: 

| sell_date  | num_sold | products                     |
| ---------- | -------- | ---------------------------- |
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |


A:  
```
select
    sell_date,
    count(product) as num_sold,
    group_concat(product order by product) as products
from
(
    select distinct sell_date, product from Activities 
) aa
group by aa.sell_date
order by aa.sell_date;
```
*This Q is about the group_concat() to store all products to one date.
I tried to put the order by product into the subquery which produced wrong answer to some test cases where the products were not stored lexicographically.*

</br>

## 1527. Patients with a condition

Table: Patients With a Condition 

| Column Name  | Type    |
| ------------ | ------- |
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |

patient_id is the primary key for this table.  
'conditions' contains 0 or more code separated by spaces.   
This table contains information of the patients in the hospital.  
Write an SQL query to report the patient_id, patient_name and conditions of the patients who have Type I Diabetes.   
Type I Diabetes always starts with DIAB1 prefix.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:    
Patients table:  

| patient_id | patient_name | conditions   |
| ---------- | ------------ | ------------ |
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |

Output: 

| patient_id | patient_name | conditions   |
| ---------- | ------------ | ------------ |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 


A:
```
select
    *
from
    Patients
where
    conditions like 'DIAB1%' or conditions like '% DIAB1%';
```
*This Q is about giving different LIKE case. I first used only the "conditions like 'DIAB1%'" but got wrong in some test cases because it would not include "ACNE DIAB100". 
I have to add "conditions like '% DIAB1%'".*


</br>

## 1965. Employees with Missing Information  

Table: Employees, Salaries

| Column Name | Type    |
| ----------- | ------- |
| employee_id | int     |
| name        | varchar |

employee_id is the primary key for this table.  
Each row of this table indicates the name of the employee whose ID is employee_id.  


| Column Name | Type    |
| ----------- | ------- |
| employee_id | int     |
| salary      | int     |

employee_id is the primary key for this table.  
Each row of this table indicates the salary of the employee whose ID is employee_id.  

Write an SQL query to report the IDs of all the employees with missing information. The information of an employee is missing if:  
	• The employee's name is missing, or  
	• The employee's salary is missing.  
Return the result table ordered by employee_id in ascending order.  
The query result format is in the following example.  
Example 1:  
Input:   
Employees table:  

| employee_id | name     |
| ----------- | -------- |
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |

Salaries table:  

| employee_id | salary |
| ----------- | ------ |
| 5           | 76071  |
| 1           | 22517  |
| 4           | 63539  |

Output: 

| employee_id |
| ----------- |
| 1           |
| 2           |

A:  
```
select
    aa.employee_id
from
(
    select
        em.employee_id, em.name, sa.salary
    from
        employees em
    left join salaries sa on em.employee_id = sa.employee_id
    union
    select
        sa.employee_id, em.name, sa.salary
    from
        employees em
    right join salaries sa on em.employee_id = sa.employee_id
)aa
where aa.name is null or aa.salary is null
order by employee_id;
```
*this answer is accepted but it beats at very low percentage, 
another way to improve it is by using the where clause in the each if the left join before union them.*


</br>


## 1795. Rearrange Products Table  

Table: Products  

| Column Name | Type    |
| ----------- | ------- |
| product_id  | int     |
| store1      | int     |
| store2      | int     |
| store3      | int     |

product_id is the primary key for this table.  
Each row in this table indicates the product's price in 3 different stores: store1, store2, and store3.  
If the product is not available in a store, the price will be null in that store's column.  
Write an SQL query to rearrange the Products table so that each row has (product_id, store, price).   
If a product is not available in a store, do not include a row with that product_id and store combination in the result table.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Products table:  

| product_id | store1 | store2 | store3 |
| ---------- | ------ | ------ | ------ |
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |

Output:  

| product_id | store  | price |
| ---------- | ------ | ----- |
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |

A:  
```
select
    product_id,
    'store1' as store,
    store1 as price
from
    Products
where store1 is not null
union
select
    product_id,
    'store2' as store,
    store2 as price
from
    Products
where store2 is not null
union
select
    product_id,
    'store3' as store,
    store3 as price
from
    Products
where store3 is not null;
```

*This Q is mainly asking us to rearrange a horizontal table into a vertical one.  
Need to understand how to use the column name as the alias name and the value from the column.*




