SQL Notes
==========

### Select top 3 without TOP/LIMIT/ROWNUM

Top 5 Quantity From OrdersDetails:
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sh
SELECT Quantity 
FROM OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity > a.Quantity) < 3
order by Quantity desc
```
or 

```sh
SELECT Quantity, (select count(*) from OrderDetails b where b.Quantity > a.Quantity) as rnk 
FROM OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity > a.Quantity) < 3
order by Quantity desc
```
Note: 
  - COUNT acts on WHERE b.Quantity > a.Quantity which is also aggregation condition 
  - Table self-join to be as a condition
  - Using select to create a new column
  - The order of b.Quantity > a.Quantity
  - Pay attention to '>=' or '>'
  - No need to use “ORDER BY” when using b.Quantity > a.Quantity

### Select top for each group

RANK() OVER (PARTITION BY column_ID ORDER BY column_ID) AS rank_id

### Second Largest 

Using LIMIT

```sh
SELECT number
FROM (
SELECT number FROM Table
ORDER BY number desc limit 2
) ORDER BY number limit 1
```

or Using TOP

```sh
SELECT TOP 1 salary   
FROM (  
SELECT TOP 2 salary  
FROM employee_table  
ORDER BY salary DESC) AS emp  
ORDER BY salary ASC;

```

Note:
  - LIMIT/TOP/OFFSET are not always available; they are not basis SQL commands. 

Using max() and not in ()

```sh
select max(Quantity) from OrderDetails
where Quantity not in (select max(Quantity) from OrderDetails)
```

Notes:
  - column IN (1,2,3) / column NOT IN (select id from table)

Using self join

```sh
select Quantity from OrderDetails a
where (select count(*) from OrderDetails b where b.Quantity > a.Quantity) = 1
```

### Maximun in each group

Maximun Quantity in each of the OrderID:
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sh
SELECT OrderDetailID, OrderID, max(Quantity) FROM OrderDetails
group by OrderID
```

Note: 
  - MAX acts on each of the group, similarly as MIN, SUM, etc. 
  
### Between Date

OrderID between '1996-09-02' and '1996-09-06':
https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all

```sh
SELECT OrderID, OrderDate FROM Orders
WHERE OrderDate BETWEEN '1996-09-02' AND '1996-09-06'
```

Note:
  - BETWEEN ... AND ... is inclusive.

or 

```sh
SELECT OrderID, OrderDate FROM Orders
WHERE OrderDate >= '1996-09-02' AND OrderDate <= '1996-09-06'
```

### Left Join

Left Join Orders and Shippers Table

```sh
SELECT a.OrderID, a.ShipperID, b.ShipperName FROM Orders a
Left join Shippers b
on a.ShipperID = b.ShipperID
```

Note:
  - “left join” fills all the records in “left table” with matches on “right table”
  




