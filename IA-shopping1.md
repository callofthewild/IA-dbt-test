```SQL
root>GRANT ALL ON shopping1.* TO 'sunbeam'@'localhost';
Query OK, 0 rows affected (0.02 sec)

C:\Users\Akash>mysql -u sunbeam -psunbeam
SET @@sql_mode='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION';

```

1. Find number of customers in each city.

```SQL
SELECT COUNT(ct.city), ct.city FROM customers AS c
INNER JOIN city_pin AS ct
WHERE c.cpin = ct.pin
GROUP BY ct.city;

sunbeam>SELECT COUNT(ct.city), ct.city FROM customers AS c
    -> INNER JOIN city_pin AS ct
    -> WHERE c.cpin = ct.pin
    -> GROUP BY ct.city;
+----------------+--------+
| COUNT(ct.city) | city   |
+----------------+--------+
|              3 | Mumbai |
|              4 | Pune   |
|              2 | Karad  |
+----------------+--------+
3 rows in set (0.01 sec)
```

2. Find number of pending and number of delivered orders whose total amount is more than 100.

```SQL
SELECT COUNT(ostatus), ostatus, SUM(oamount)
FROM orders
WHERE ostatus = 'pending' OR ostatus = 'delivered'
GROUP BY ostatus
HAVING SUM(oamount) > 100;

sunbeam>SELECT COUNT(ostatus), ostatus, SUM(oamount)
    -> FROM orders
    -> WHERE ostatus = 'pending' OR ostatus = 'delivered'
    -> GROUP BY ostatus
    -> HAVING SUM(oamount) > 100;
+----------------+-----------+--------------+
| COUNT(ostatus) | ostatus   | SUM(oamount) |
+----------------+-----------+--------------+
|              4 | delivered |      1050.00 |
|              1 | pending   |       130.00 |
+----------------+-----------+--------------+
2 rows in set (0.02 sec)
```

3. Display top 3 products that produce highest revenue (quantity \* rate).
4. Find total number of orders by customer John, Mac and Donald.
5. Mark last order of John as Delivered.
6. Display the customer name and their total order amount where order status is
   delivered and total is more than 300
7. Update the last order of Adam from Pending to dispatch and then dispatch to
   Delivered (in two different queries).
8. Display pins and cities of all customers whose name start and end with a
   vowel (a, e, i, o, u).
9. Display cities, number of orders and total amount of orders from cities in asc
   order of city name.
10. Print total order amount from each city in descending order of amount.
