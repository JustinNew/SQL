SQL Notes
==========

## Select top 3 without TOP/LIMIT/ROWNUM

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

