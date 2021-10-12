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

```SQL
SELECT p.pid, p.pname, p.prate, SUM(oi.pqty), p.prate * SUM(oi.pqty) AS total
FROM products AS p
INNER JOIN order_items AS oi
ON oi.pid = p.pid
GROUP BY p.pid, p.pname, p.prate
ORDER BY total DESC
LIMIT 3;

sunbeam>SELECT p.pid, p.pname, p.prate, SUM(oi.pqty), p.prate * SUM(oi.pqty) AS total
    -> FROM products AS p
    -> INNER JOIN order_items AS oi
    -> ON oi.pid = p.pid
    -> GROUP BY p.pid, p.pname, p.prate
    -> ORDER BY total DESC
    -> LIMIT 3;
+------+-----------+-------+--------------+-------+
| pid  | pname     | prate | SUM(oi.pqty) | total |
+------+-----------+-------+--------------+-------+
|    4 | Notebook  |    60 |           14 |   840 |
|    6 | Marker    |    30 |           15 |   450 |
|    5 | SketchPen |    25 |           10 |   250 |
+------+-----------+-------+--------------+-------+
3 rows in set (0.01 sec)
```

4. Find total number of orders by customer John, Mac and Donald.

```SQL
SELECT oid, odate, ostatus, oamount, cid, COUNT(oid)
FROM orders
WHERE orders.cid IN
    (SELECT cid FROM customers
    WHERE cname IN ('John', 'Mac', 'Donald'))
GROUP BY oid;
```

5. Mark last order of John as Delivered.

```SQL
UPDATE orders
SET ostatus = 'delivered'
WHERE cid =
    (SELECT cid FROM customers
    WHERE cname = 'John');

sunbeam>UPDATE orders
    -> SET ostatus = 'delivered'
    -> WHERE cid =
    ->     (SELECT cid FROM customers
    ->     WHERE cname = 'John');
Query OK, 1 row affected (0.05 sec)
Rows matched: 1  Changed: 1  Warnings: 0

sunbeam>select * from orders;
+-----+------------+------------+---------+------+
| oid | odate      | ostatus    | oamount | cid  |
+-----+------------+------------+---------+------+
|   1 | 2021-10-02 | delivered  |  150.00 |  101 |
|   2 | 2021-10-02 | dispatched |   50.00 |  102 |
|   3 | 2021-10-03 | dispatched |  450.00 |  104 |
|   4 | 2021-10-03 | delivered  |  450.00 |  119 |
|   5 | 2021-10-02 | delivered  |  100.00 |  105 |
|   6 | 2021-10-05 | delivered  |  150.00 |  108 |
|   7 | 2021-10-02 | delivered  |  350.00 |  108 |
|   8 | 2021-10-01 | dispatched |   90.00 |  103 |
|   9 | 2021-10-01 | pending    |  130.00 |  107 |
+-----+------------+------------+---------+------+
9 rows in set (0.05 sec)
```

6. Display the customer name and their total order amount where order status is delivered and total is more than 300

```SQL
SELECT c.cname, SUM(o.oamount)
FROM customers AS c
INNER JOIN orders AS o
ON o.cid = c.cid
WHERE o.ostatus = 'delivered'
GROUP BY o.cid, c.cname
HAVING SUM(oamount) > 300;

sunbeam>SELECT c.cname, SUM(o.oamount)
    -> FROM customers AS c
    -> INNER JOIN orders AS o
    -> ON o.cid = c.cid
    -> WHERE o.ostatus = 'delivered'
    -> GROUP BY o.cid, c.cname
    -> HAVING SUM(oamount) > 300;
+--------+----------------+
| cname  | SUM(o.oamount) |
+--------+----------------+
| Millar |         500.00 |
| Merry  |         450.00 |
+--------+----------------+
2 rows in set (0.00 sec)
```

7. Update the last order of Adam from Pending to dispatch and then dispatch to Delivered (in two different queries).

```SQL
UPDATE orders
SET ostatus = 'dispatched'
WHERE cid =
    (SELECT cid FROM customers
    WHERE cname = 'Adam');

UPDATE orders
SET ostatus = 'delivered'
WHERE cid =
    (SELECT cid FROM customers
    WHERE cname = 'Adam');

sunbeam>SELECT * FROM orders;
+-----+------------+------------+---------+------+
| oid | odate      | ostatus    | oamount | cid  |
+-----+------------+------------+---------+------+
|   1 | 2021-10-02 | delivered  |  150.00 |  101 |
|   2 | 2021-10-02 | dispatched |   50.00 |  102 |
|   3 | 2021-10-03 | dispatched |  450.00 |  104 |
|   4 | 2021-10-03 | delivered  |  450.00 |  119 |
|   5 | 2021-10-02 | delivered  |  100.00 |  105 |
|   6 | 2021-10-05 | delivered  |  150.00 |  108 |
|   7 | 2021-10-02 | delivered  |  350.00 |  108 |
|   8 | 2021-10-01 | dispatched |   90.00 |  103 |
|   9 | 2021-10-01 | delivered  |  130.00 |  107 |
+-----+------------+------------+---------+------+
9 rows in set (0.00 sec)
```

8. Display pins and cities of all customers whose name start and end with a vowel (a, e, i, o, u).

```SQL
SELECT c.cname, c.cpin, cp.city
FROM customers AS c
INNER JOIN city_pin AS cp
ON c.cpin = cp.pin
WHERE LEFT(LOWER(c.cname), 1) IN ('a', 'e', 'i', 'o', 'u')
AND RIGHT(LOWER(c.cname), 1) IN ('a', 'e', 'i', 'o', 'u');

sunbeam>SELECT c.cname, c.cpin, cp.city
    -> FROM customers AS c
    -> INNER JOIN city_pin AS cp
    -> ON c.cpin = cp.pin
    -> WHERE LEFT(LOWER(c.cname), 1) IN ('a', 'e', 'i', 'o', 'u')
    -> AND RIGHT(LOWER(c.cname), 1) IN ('a', 'e', 'i', 'o', 'u');
+--------+--------+--------+
| cname  | cpin   | city   |
+--------+--------+--------+
| Ileana | 400027 | Mumbai |
+--------+--------+--------+
1 row in set (0.00 sec)
```

9. Display cities, number of orders and total amount of orders from cities in asc order of city name.

```SQL
SELECT cp.city, COUNT(cp.city)
FROM city_pin AS cp
GROUP BY cp.city
ORDER BY cp.city ASC;


SELECT cp.city, c.cid
FROM city_pin AS cp
INNER JOIN customers AS c
ON c.cpin = cp.pin;


SELECT cp.city, c.cid, o.oid
FROM city_pin AS cp
INNER JOIN customers AS c
ON c.cpin = cp.pin
INNER JOIN orders AS o
ON o.cid = c.cid;


SELECT cp.city, c.cid, o.oid, p.prate
FROM city_pin AS cp
INNER JOIN customers AS c
ON c.cpin = cp.pin
INNER JOIN orders AS o
ON o.cid = c.cid
INNER JOIN order_items AS oi
ON o.oid = oi.oid
INNER JOIN products AS p
ON oi.pid = p.pid;


SELECT cp.city, COUNT(oi.oid)
FROM city_pin AS cp
INNER JOIN customers AS c
ON c.cpin = cp.pin
INNER JOIN orders AS o
ON o.cid = c.cid
INNER JOIN order_items AS oi
ON o.oid = oi.oid
INNER JOIN products AS p
ON oi.pid = p.pid
GROUP BY cp.city;

SELECT cp.city, COUNT(oi.oid), SUM(p.prate * oi.pqty)
FROM city_pin AS cp
INNER JOIN customers AS c
ON c.cpin = cp.pin
INNER JOIN orders AS o
ON o.cid = c.cid
INNER JOIN order_items AS oi
ON o.oid = oi.oid
INNER JOIN products AS p
ON oi.pid = p.pid
GROUP BY cp.city
ORDER BY cp.city ASC;

sunbeam>SELECT cp.city, COUNT(oi.oid), SUM(p.prate * oi.pqty)
    -> FROM city_pin AS cp
    -> INNER JOIN customers AS c
    -> ON c.cpin = cp.pin
    -> INNER JOIN orders AS o
    -> ON o.cid = c.cid
    -> INNER JOIN order_items AS oi
    -> ON o.oid = oi.oid
    -> INNER JOIN products AS p
    -> ON oi.pid = p.pid
    -> GROUP BY cp.city
    -> ORDER BY cp.city ASC;
+--------+---------------+------------------------+
| city   | COUNT(oi.oid) | SUM(p.prate * oi.pqty) |
+--------+---------------+------------------------+
| Karad  |             5 |                    630 |
| Mumbai |             5 |                    600 |
| Pune   |             4 |                    240 |
+--------+---------------+------------------------+
3 rows in set (0.00 sec)
```

10. Print total order amount from each city in descending order of amount.
