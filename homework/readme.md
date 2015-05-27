readme.

<!-- sqlite3

.open store.sqlite3

.tables
addresses          orders             users
items              schema_migrations

.headers on
SELECT * FROM addresses;
SELECT * FROM orders;
SELECT * FROM users;
ELECT * FROM items;
SELECT * FROM schema_migrations; -->

Normal Mode:
1.How many users are there?

SELECT COUNT(id) FROM users;
50


2.What are the 5 most expensive items?

SELECT * FROM items ORDER BY PRICE DESC LIMIT 5;
Small Cotton Gloves
Small Wooden Computer
Awesome Granite Pants
Sleek Wooden Hat
Ergonomic Steel Car


3.What’s the cheapest book? (Does that change for “category is exactly ‘book’” versus “category contains ‘book’”?)

SELECT * FROM items WHERE category LIKE '%book%' ORDER BY PRICE LIMIT 1;
"Ergonomic Granite Chair"

SELECT * FROM items WHERE category = 'Books' ORDER BY PRICE LIMIT 1;
No. It is then smae book: "Ergonomic Granite Chair"


4.Who lives at “6439 Zetta Hills, Willmouth, WY”? Do they have another address?

SELECT * FROM addresses WHERE street='6439 Zetta Hills'AND city='Willmouth'AND state='WY';
id|user_id|street|city|state|zip
43|40|6439 Zetta Hills|Willmouth|WY|15029

SELECT * FROM addresses WHERE user_id='40';
Yes. Another adress is "54369 Wolff Forges|Lake Bryon|CA|31587"


5.Correct Virginie Mitchell’s address to “New York, NY, 10108”.

SELECT * FROM users WHERE first_name='Virginie' AND last_name='Mitchell';
id|first_name|last_name|email
39|Virginie|Mitchell|daisy.crist@altenwerthmonahan.biz

UPDATE addresses SET city='New York',state='NY',zip='10108' WHERE user_id ='39';

SELECT * FROM addresses WHERE user_id = '39';
id|user_id|street|city|state|zip
41|39|12263 Jake Crossing|New York|NY|10108
42|39|83221 Mafalda Canyon|New York|NY|10108


6.How much would it cost to buy one of each tool?

SELECT SUM(price) FROM items WHERE category = 'Tools';
7383


7.How many total items did we sell?

SELECT * FROM items WHERE category = 'Tools';
id|title|category|description|price
32|Practical Rubber Shirt|Tools|De-engineered multimedia info-mediaries|1107
80|Incredible Plastic Gloves|Tools|Operative mission-critical emulation|5437
87|Awesome Plastic Shirt|Tools|Balanced multimedia paradigm|83

SELECT SUM(quantity) FROM orders WHERE item_id = 32 OR 80 OR 87;
2125

<!-- SELECT AVG(price) FROM items WHERE category = 'Tools'; -->


8.How much was spent on books?

SELECT SUM(price * quantity) FROM orders join items ON orders.item_id = items.id Where category = 'Books';
420566

<!--
SELECT * FROM items WHERE category = 'Books';
id|title|category|description|price
4|Fantastic Steel Chair|Books|Advanced attitude-oriented encryption|9246
21|Fantastic Rubber Shoes|Books|Reverse-engineered modular hierarchy|8904
76|Ergonomic Granite Chair|Books|De-engineered bi-directional portal|1496
98|Practical Plastic Hat|Books|Implemented non-volatile model|3056 -->


<!-- SELECT SUM(orders.quantity * items.price)
FROM orders, items
WHERE orders.item_id = 4 and items.id = 4;
Toal is 46230

SELECT SUM(orders.quantity * items.price)
FROM orders, items
WHERE orders.item_id = 21 and items.id = 21;
Toal is 293832

SELECT SUM(orders.quantity * items.price)
FROM orders, items
WHERE orders.item_id = 76 and items.id = 76;
Total is 22440

SELECT SUM(orders.quantity * items.price)
FROM orders, items
WHERE orders.item_id = 98 and items.id = 98;
Total is 58064

SELECT SUM(*)
FROM (SELECT SUM(orders.quantity * items.price)
FROM orders, items WHERE orders.item_id = orders.item_id = 98 or orders.item_id = 76  )
WHERE orders.item_id = 98 AND orders.item_id = 76;
 -->


9.Simulate buying an item by inserting a User for yourself and an Order for that User.

INSERT INTO users (id, first_name,last_name,email)
VALUES (51, 'WEI', 'XIA','graciexia@outlook.com');

INSERT INTO orders (id, user_id, item_id, quantity, created_at)
VALUES (378, 51, 91, 20, '2015-02-09 00:45:31.307668');





Hard Mode:
10. What item was ordered most often? Grossed the most money?

SELECT title, item_id, count(item_id) FROM items join orders ON orders.item_id = items.id Group by orders.item_id ORDER BYcount(item_id) DESC LIMIT 5;
Ergonomic Concrete Gloves  10          9
Practical Rubber Computer  46          9
Incredible Granite Car     65          9

SELECT title, item_id, count(item_id),price,sum(quantity), price * sum(quantity) FROM items join orders ON orders.item_id = items.id Group by orders.item_id ORDER BY price * sum(quantity) DESC LIMIT 5;
title                   item_id     count(item_id)  price       sum(quantity)  price*sum(quantity)
----------------------  ----------  --------------  ----------  -------------  -------------------
Incredible Granite Car  65          9               7295        72             525240


11. What user spent the most?

SELECT first_name, last_name, users.id "user id", items.id "item id", price, sum(price*quantity)
FROM users
JOIN orders ON users.id = orders.user_id
JOIN items ON orders.item_id = items.id
Group by users.id
ORDER BY sum(price*quantity) DESC LIMIT 5;
first_name  last_name   user id     item id     price       sum(price*quantity)
----------  ----------  ----------  ----------  ----------  -------------------
Hassan      Runte       19          85          4849        639386


12. What were the top 3 highest grossing categories?
SELECT category, count(items.id) "number of orders", sum(quantity) "total items ordered", sum(price*quantity)
FROM orders
JOIN items ON orders.item_id = items.id
Group by category
ORDER BY sum(price*quantity) DESC LIMIT 3;
category                  number of orders  total items ordered  sum(price*quantity)
------------------------  ----------------  -------------------  -------------------
Music, Sports & Clothing  9                 72                   525240
Beauty, Toys & Sports     7                 54                   449496
Sports                    17                102                  448410
