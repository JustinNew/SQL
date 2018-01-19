SQL Notes
==========

### Select top 3 without TOP/LIMIT/ROWNUM

Top 3 Quantity From OrdersDetails:
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT Quantity 
FROM OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity >= a.Quantity) <= 3
order by Quantity desc
```
or 

```sql
SELECT Quantity, (select count(*) from OrderDetails b where b.Quantity >= a.Quantity) as rnk 
FROM OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity >= a.Quantity) <= 3
order by Quantity desc
```
Note: 
  - COUNT acts on WHERE b.Quantity >= a.Quantity which is also aggregation condition 
  - Table self-join to be as a condition
  - Using select to create a new column
  - The order of b.Quantity > a.Quantity
  - Pay attention to '>=' or '>'
  - No need to use “ORDER BY” when using b.Quantity >= a.Quantity

### Second Largest 

Using **LIMIT**

```sql
SELECT number
FROM (
SELECT number FROM Table
ORDER BY number desc limit 2
) ORDER BY number limit 1
```

or Using **TOP**

```sql
SELECT TOP 1 salary   
FROM (  
SELECT TOP 2 salary  
FROM employee_table  
ORDER BY salary DESC) AS emp  
ORDER BY salary ASC;
```

or Using **LIMIT** and **OFFSET**
```sql
SELECT OrderID, Quantity FROM OrderDetails
Order By Quantity desc
Limit 1 Offset 1
```

Note:
  - **LIMIT/TOP/OFFSET** are not always available; they are not basis SQL commands. 

Using **max()** and **not in ()**

```sql
select max(Quantity) from OrderDetails
where Quantity not in (select max(Quantity) from OrderDetails)
```

Notes:
  - **column IN (1,2,3)** / **column NOT IN (select id from table)**

Using **self join**

```sql
select Quantity from OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity > a.Quantity) = 1
```

or 
```sql
SELECT OrderID, Quantity FROM OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity >= a.Quantity) = 2
```

### Maximun in each group

Using **rank() over()**

```sql
RANK() OVER (PARTITION BY column_ID ORDER BY column_ID) AS rank_id
```

Maximun Quantity in each of the *OrderID*:
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT OrderDetailID, OrderID, max(Quantity) FROM OrderDetails
group by OrderID
```

Note: 
  - **MAX** acts on each of the group, similarly as **MIN**, **SUM**, etc. 
  
### Between Date

*OrderID* between *'1996-09-02'* and *'1996-09-06'*:
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT OrderID, OrderDate FROM Orders
WHERE OrderDate BETWEEN '1996-09-02' AND '1996-09-06'
```

Note:
  - **BETWEEN ... AND ...** is inclusive.

or 

```sql
SELECT OrderID, OrderDate FROM Orders
WHERE OrderDate >= '1996-09-02' AND OrderDate <= '1996-09-06'
```

### Left Join

Left Join **Orders** on **Shippers**
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT a.OrderID, a.ShipperID, b.ShipperName FROM Orders a
Left join Shippers b
on a.ShipperID = b.ShipperID
```

Note:
  - **left join** fills all the records in “left table” with matches on “right table”
  

Left Join **Shippers** on **Orders**
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT a.ShipperID, ShipperName, OrderID, OrderDate
FROM Shippers a
LEFT JOIN Orders b
ON a.ShipperID = b.ShipperID
```

Note:
  - If there are multiply copies in the ‘right table’ matching the ‘left table’. The ‘left table’ matched item will duplicate to have multiply copies.
  - There is no difference between ‘left join’ and ‘join’ if there is no ‘missing’ on the join condition element(s) in the two tables. The following three queries return the same results (order may be different).
  - ```sql
	SELECT a.ShipperName, CustomerID, EmployeeID, OrderID FROM Shippers a
	join Orders b
	on a.ShipperID = b.ShipperID
    ```
  - ```sql
	SELECT a.ShipperName, CustomerID, EmployeeID, OrderID FROM Shippers a
	Left join Orders b
	on a.ShipperID = b.ShipperID
    ```
  - ```sql
	SELECT b.ShipperName, CustomerID, EmployeeID, OrderID FROM Orders a
	Left join Shippers b
	on a.ShipperID = b.ShipperID
    ```

### String Match

Write an SQL query to find names of employee start with 'A'?

```sql
SELECT * FROM Employees WHERE EmpName like 'A%'
```

or end with 'A'
```sql
SELECT * FROM Employees WHERE EmpName like '%A'
```

or 'A' in the second position
```sql
SELECT * FROM Employees WHERE EmpName like '_A%'
```

### Where vs Having

Select the *EmployeeID* with the number of orders larger than 10 from **Orders**
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
SELECT EmployeeID, count(OrderID) FROM Orders
group by EmployeeID
having count(OrderID) > 10
```

Note:
  - “WHERE” vs “HAVING”: The WHERE clause cannot be used to restrict groups. The HAVING clause should be used.
  - “HAVING” need to be used after ‘GROUP BY’
  
### Most common elements in a column

The most frequent *Quantity* in **OrderDetails**
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
select Quantity, max(cnt) from (
SELECT Quantity, count(Quantity) as cnt FROM [OrderDetails]
group by Quantity) a
```

Second most frequent *Quantity* in **OrderDetails**
```sql
select Quantity, max(cnt) from (
SELECT Quantity, count(Quantity) as cnt FROM [OrderDetails]
group by Quantity) a
where cnt not in (
select max(cnt) from (
SELECT Quantity, count(Quantity) as cnt FROM [OrderDetails]
group by Quantity)
)
```
or
```sql
select Quantity, cnt from (
SELECT Quantity, count(Quantity) as cnt FROM [OrderDetails]
group by Quantity) a
order by cnt desc
limit 1 offset 1
```

### Accumulative Sum 

Get the first *OrderDetailID* from which the accumulative number of orders larger than 80 from **OrderDetails**
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sql
select min(OrderDetailID), accsum from (
SELECT OrderDetailID, (select sum(Quantity) from OrderDetails b where b.OrderDetailID <= a.OrderDetailID) as accsum FROM OrderDetails a
)
where accsum > 80
```

Note:
  - Using self join
  - Using sum()
  - Pay attention to *<* or *<=* depending on question
