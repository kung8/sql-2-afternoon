<img src="https://s3.amazonaws.com/devmountain/readme-logo.png" width="250" align="right">

# Project Summary

In this project, we'll continue to use <a href="https://postgres.devmountain.com/">postgres.devmountain.com</a> to practice joins, nested queries, updating rows, group by, distinct, and foreign keys.

Any new tables or records that you add into the database will be removed after you refresh the page.

Use <a href="http://www.sqlteaching.com">SQL Teaching</a> or <a href="http://www.sqlbolt.com">SQL Bolt</a> as resources for referencing on keywords you'll need.

## Practice joins

### Instructions

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
SELECT [Column names] 
FROM [table] [abbv]
JOIN [table2] [abbv2] ON abbv.prop = abbv2.prop WHERE [Conditions];

SELECT a.name, b.name FROM some_table a JOIN another_table b ON a.some_id = b.some_id;
SELECT a.name, b.name FROM some_table a JOIN another_table b ON a.some_id = b.some_id WHERE b.email = 'e@mail.com';
```

</details>

<br />

1. Get all invoices where the `unit_price` on the `invoice_line` is greater than $0.99.

SELECT * 
FROM invoice as i 
JOIN invoice_line il ON il.invoice_id = i.invoice_id
WHERE unit_price > 0.99

2. Get the `invoice_date`, customer `first_name` and `last_name`, and `total` from all invoices.

SELECT invoice_date,first_name,last_name, total
FROM invoice_line
JOIN invoice on invoice.invoice_id = invoice_line.invoice_id
JOIN customer on customer.customer_id = invoice.customer_id

3. Get the customer `first_name` and `last_name` and the support rep's `first_name` and `last_name` from all customers. 
    * Support reps are on the employee table.

SELECT emp.first_name as emp_first_name, emp.last_name as emp_last_name, c.first_name as cust_first_name, c.last_name  as cust_last_name
FROM employee as emp
JOIN customer c on c.support_rep_id = emp.employee_id

4. Get the album `title` and the artist `name` from all albums.

SELECT t`itle as album_name, name as artist_name
FROM artist
JOIN album ON album.artist_id = artist.artist_id


5. Get all playlist_track track_ids where the playlist `name` is Music.

SELECT p.name, p_t.track_id 
FROM playlist p
JOIN playlist_track p_t ON p_t.playlist_id = p.playlist_id
WHERE name = 'Music'

6. Get all track `name`s for `playlist_id` 5.

SELECT track.name,playlist_track.playlist_id 
FROM playlist_track
JOIN track ON track.track_id = playlist_track.track_id
WHERE playlist_id = 5

7. Get all track `name`s and the playlist `name` that they're on ( 2 joins ).

SELECT t.name as track_name, p.name as playlist_name
FROM track t
JOIN playlist_track p_t ON p_t.track_id = t.track_id 
JOIN playlist p ON p.playlist_id = p_t.playlist_id


8. Get all track `name`s and album `title`s that are the genre `Alternative & Punk` ( 2 joins ).

SELECT t.name as track_name, a.title as album_title
FROM track t
JOIN album a ON a.album_id = t.album_id
JOIN genre g ON g.genre_id = t.genre_id
WHERE g.name = 'Alternative & Punk' 

### Solution

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
SELECT *
FROM invoice i
JOIN invoice_line il ON il.invoice_id = i.invoice_id
WHERE il.unit_price > 0.99;
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
SELECT i.invoice_date, c.first_name, c.last_name, i.total
FROM invoice i
JOIN customer c ON i.customer_id = c.customer_id;
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
SELECT c.first_name, c.last_name, e.first_name, e.last_name
FROM customer c
JOIN employee e ON c.support_rep_id = e.employee_id;
```

</details>

<details>

<summary> <code> #4 </code> </summary>

```sql
SELECT al.title, ar.name
FROM album al
JOIN artist ar ON al.artist_id = ar.artist_id;
```

</details>

<details>

<summary> <code> #5 </code> </summary>

```sql
SELECT pt.track_id
FROM playlist_track pt
JOIN playlist p ON p.playlist_id = pt.playlist_id
WHERE p.name = 'Music';
```

</details>

<details>

<summary> <code> #6 </code> </summary>

```sql
SELECT t.name
FROM track t
JOIN playlist_track pt ON pt.track_id = t.track_id
WHERE pt.playlist_id = 5;
```

</details>

<details>

<summary> <code> #7 </code> </summary>

```sql
SELECT t.name, p.name
FROM track t
JOIN playlist_track pt ON t.track_id = pt.track_id
JOIN playlist p ON pt.playlist_id = p.playlist_id;
```

</details>

<details>

<summary> <code> #8 </code> </summary>

```sql
SELECT t.name, a.title
FROM track t
JOIN album a ON t.album_id = a.album_id
JOIN genre g ON g.genre_id = t.genre_id
WHERE g.name = 'Alternative & Punk';
```

</details>

</details>

### Black Diamond

* Get all tracks on the playlist(s) called Music and show their name, genre name, album name, and artist name.
  * At least 5 joins.

SELECT track.name, genre.name,album.title, artist.name
FROM track 
JOIN genre ON genre.genre_id = track.genre_id
JOIN album ON album.album_id = track.album_id
JOIN artist ON artist.artist_id = album.artist_id
JOIN playlist_track ON playlist_track.track_id = track.track_id
JOIN playlist ON playlist.playlist_id = playlist_track.playlist_id
WHERE playlist.name = 'Music'

## Practice nested queries

### Summary

Complete the instructions without using any joins. Only use nested queries to come up with the solution.

### Instructions

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
SELECT [column names] 
FROM [table] 
WHERE column_id IN ( SELECT column_id FROM [table2] WHERE [Condition] );

SELECT name, Email FROM Athlete WHERE AthleteId IN ( SELECT PersonId FROM PieEaters WHERE Flavor='Apple' );
```

</details>

<br />

1. Get all invoices where the `unit_price` on the `invoice_line` is greater than $0.99.

SELECT * 
FROM invoice
WHERE invoice_id IN (
  SELECT invoice_id 
  FROM invoice_line 
  WHERE unit_price > 0.99)

2. Get all playlist tracks where the playlist name is Music.

SELECT * 
FROM playlist_track 
WHERE playlist_id IN (
  SELECT playlist_id
  FROM playlist 
  WHERE name = 'Music')

3. Get all track names for `playlist_id` 5.

SELECT name
FROM track
WHERE track_id IN (
  SELECT track_id 
  FROM playlist_track 
  WHERE playlist_id = 5) 

4. Get all tracks where the `genre` is Comedy.

SELECT *
FROM track
WHERE genre_id IN (
  SELECT genre_id
  FROM genre
  WHERE name = 'Comedy')

5. Get all tracks where the `album` is Fireball.

SELECT * 
FROM track 
WHERE album_id IN (
  SELECT album_id 
  FROM album 
  WHERE title = 'Fireball')

6. Get all tracks for the artist Queen ( 2 nested subqueries ).

SELECT * 
FROM track 
WHERE album_id IN (
  SELECT album_id 
  FROM album 
  WHERE artist_id IN (
    SELECT artist_id 
    FROM artist
    WHERE name = 'Queen'))

### Solution

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
SELECT *
FROM invoice
WHERE invoice_id IN ( SELECT invoice_id FROM invoice_line WHERE unit_price > 0.99 );
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
SELECT *
FROM playlist_track
WHERE playlist_id IN ( SELECT playlist_id FROM playlist WHERE name = 'Music' );
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
SELECT name
FROM track
WHERE track_id IN ( SELECT track_id FROM playlist_track WHERE playlist_id = 5 );
```

</details>

<details>

<summary> <code> #4 </code> </summary>

```sql
SELECT *
FROM track
WHERE genre_id IN ( SELECT genre_id FROM genre WHERE name = 'Comedy' );
```

</details>

<details>

<summary> <code> #5 </code> </summary>

```sql
SELECT *
FROM track
WHERE album_id IN ( SELECT album_id FROM album WHERE title = 'Fireball' );
```

</details>

<details>

<summary> <code> #6 </code> </summary>

```sql
SELECT *
FROM track
WHERE album_id IN ( 
  SELECT album_id FROM album WHERE artist_id IN ( 
    SELECT artist_id FROM artist WHERE name = 'Queen'
  )
); 
```

</details>

</details>

## Practice updating Rows

### Instructions

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
UPDATE [table] 
SET [column1] = [value1], [column2] = [value2] 
WHERE [Condition];

UPDATE athletes SET sport = 'Picklball' WHERE sport = 'pockleball';
```

</details>

<br />

1. Find all customers with fax numbers and set those numbers to `null`.

UPDATE customer 
SET fax = null
WHERE fax IS NOT null;

2. Find all customers with no company (null) and set their company to `"Self"`.

UPDATE customer 
SET company = 'Self'
where company = null;

3. Find the customer `Julia Barnett` and change her last name to `Thompson`.

UPDATE customer 
SET last_name = 'Thompson'
WHERE first_name = 'Julia' AND last_name = 'Barnett'

4. Find the customer with this email `luisrojas@yahoo.cl` and change his support rep to `4`.

UPDATE customer 
SET support_rep_id = 4
WHERE email = 'luisrojas@yahoo.cl'

5. Find all tracks that are the genre `Metal` and have no composer. Set the composer to `"The darkness around us"`.

UPDATE track 
SET composer = 'The darkness around us'
WHERE composer is null and genre_id in (
	SELECT genre_id
	FROM genre
	WHERE name = 'Metal'); 


//check if this worked remove the composer is null
SELECT * 
FROM track
WHERE genre_id in (
	SELECT genre_id
	FROM genre
	WHERE name = 'Metal')  
	and composer is null 

6. Refresh your page to remove all database changes.

### Solution

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
UPDATE customer
SET fax = null
WHERE fax IS NOT null;
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
UPDATE customer
SET company = 'Self'
WHERE company IS null;
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
UPDATE customer 
SET last_name = 'Thompson' 
WHERE first_name = 'Julia' AND last_name = 'Barnett';
```

</details>

<details>

<summary> <code> #4 </code> </summary>

```sql
UPDATE customer
SET support_rep_id = 4
WHERE email = 'luisrojas@yahoo.cl';
```

</details>

<details>

<summary> <code> #5 </code> </summary>

```sql
UPDATE track
SET composer = 'The darkness around us'
WHERE genre_id = ( SELECT genre_id FROM genre WHERE name = 'Metal' )
AND composer IS null;
```

</details>

</details>

## Group by

### Instructions

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
SELECT [column1], [column2]
FROM [table] [abbr]
GROUP BY [column];
```

</details>

<br />

1. Find a count of how many tracks there are per genre. Display the genre name with the count.

SELECT COUNT(track.genre_id), genre.name
FROM track
JOIN genre on genre.genre_id = track.genre_id
GROUP BY genre.name

2. Find a count of how many tracks are the `"Pop"` genre and how many tracks are the `"Rock"` genre.

SELECT COUNT(track.genre_id), genre.name
FROM track
JOIN genre on genre.genre_id = track.genre_id
WHERE genre.name IN ('Rock','Pop') 
GROUP BY genre.name

3. Find a list of all artists and how many albums they have.

SELECT COUNT(album) as album_count,name
FROM album 
JOIN artist on artist.artist_id = album.artist_id
GROUP BY name
order by name asc;

### Solution

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
SELECT COUNT(*), g.name
FROM track t
JOIN genre g ON t.genre_id = g.genre_id
GROUP BY g.name;
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
SELECT COUNT(*), g.name
FROM track t
JOIN genre g ON g.genre_id = t.genre_id
WHERE g.name = 'Pop' OR g.name = 'Rock'
GROUP BY g.name;
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
SELECT ar.name, COUNT(*)
FROM album al
JOIN artist ar ON ar.artist_id = al.artist_id
GROUP BY ar.name;
```

</details>

</details>

## Use Distinct

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
SELECT DISTINCT [column]
FROM [cable];
```

</details>

<br />

1. From the `track` table find a unique list of all `composer`s.

SELECT distinct composer, *
FROM track 

2. From the `invoice` table find a unique list of all `billing_postal_code`s.

SELECT distinct billing_postal_code
FROM invoice 

3. From the `customer` table find a unique list of all `company`s.

SELECT distinct company
FROM customer

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
SELECT DISTINCT composer
FROM track;
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
SELECT DISTINCT billing_postal_code
FROM invoice;
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
SELECT DISTINCT company
FROM customer;
```

</details>

</details>

## Delete Rows

### Summary

Always do a select before a delete to make sure you get back exactly what you want and only what you want to delete! Since we cannot delete anything from the pre-defined tables ( foreign key restraints ), use the following SQL code to create a dummy table:

<details>

<summary> <code> practice_delete TABLE </code> </summary>

```sql
CREATE TABLE practice_delete ( name TEXT, type TEXT, value INTEGER );
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'bronze', 50);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'bronze', 50);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'bronze', 50);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'silver', 100);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'silver', 100);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'gold', 150);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'gold', 150);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'gold', 150);
INSERT INTO practice_delete ( name, type, value ) VALUES ('delete', 'gold', 150);

SELECT * FROM practice_delete;
```

</details>

### Instructions

<details>

<summary> <code> Syntax Hint </code> </summary>

```sql
DELETE FROM [table] WHERE [condition]
```

</details>

<br />

1. Copy, paste, and run the SQL code from the summary.
2. Delete all `'bronze'` entries from the table.

DELETE
FROM practice_delete
WHERE type = 'bronze'

3. Delete all `'silver'` entries from the table.

DELETE
FROM practice_delete
WHERE type = 'silver'

4. Delete all entries whose value is equal to `150`.

delete
FROM practice_delete
WHERE value = 150

### Solution

<details>

<summary> <code> SQL Solutions </code> </summary>

<details>

<summary> <code> #1 </code> </summary>

```sql
DELETE 
FROM practice_delete 
WHERE type = 'bronze';
```

</details>

<details>

<summary> <code> #2 </code> </summary>

```sql
DELETE 
FROM practice_delete 
WHERE type = 'silver';
```

</details>

<details>

<summary> <code> #3 </code> </summary>

```sql
DELETE 
FROM practice_delete 
WHERE value = 150;
```

</details>

</details>


## eCommerce Simulation - No Hints

### Summary

Let's simulate an e-commerce site. We're going to need users, products, and orders.

* users need a name and an email.
* products need a name and a price
* orders need a ref to product.
* All 3 need primary keys.

### Instructions

* Create 3 tables following the criteria in the summary.

CREATE TABLE users (
  user_id SERIAL PRIMARY KEY, 
  name VarChar(30) NOT NULL, 
  email VarChar(50) NOT NULL
);

CREATE TABLE products (
  product_id SERIAL PRIMARY KEY, 
  name VarChar(30) NOT NULL, 
  price float NOT NULL
);

CREATE TABLE orders (
  order_id SERIAL PRIMARY KEY, 
  product_id INT REFERENCES products(product_id) NOT NULL
);

* Add some data to fill up each table.

INSERT INTO products (name,price)
VALUES 	('MacBook Air','500'),
				('MacBook Pro','800'),
        ('iMac','400')
				

INSERT INTO users (name,email)
VALUES 	('John','john@yahoo.com'),
				('Peter','peter@yahoo.com'),
        ('James','jame@yahoo.com')
				

INSERT INTO orders (product_id)
VALUES 	(1),
				(2),
        (3)

  * At least 3 users, 3 products, 3 orders.
* Run queries against your data.
  * Get all products for the first order.

SELECT * 
FROM orders
WHERE order_id = 1

  * Get all orders.

SELECT * 
FROM orders

  * Get the total cost of an order ( sum the price of all products on an order ).

SELECT SUM(price) 
FROM orders
JOIN products ON products.product_id = orders.product_id
WHERE order_id = 1

* Add a foreign key reference from orders to users.

ALTER TABLE orders
ADD COLUMN user_id INT REFERENCES users(user_id)

* Update the orders table to link a user to each order.

UPDATE orders 
SET user_id = 3
WHERE order_id = 1;

UPDATE orders 
SET user_id = 2
WHERE order_id = 3;

UPDATE orders 
SET user_id = 1
WHERE order_id = 2;

* Run queries against your data.
  * Get all orders for a user.

added a qty column
-- ALTER TABLE orders
-- ADD COLUMN qty INT;

added a bunch of dummy data
INSERT INTO orders (product_id, user_id, qty)
VALUES 	(3,3,4),
        (2,3,1),
      	(1,1,7),
      	(2,2,2),
      	(3,2,5)

  * Get how many orders each user has.



### Black Diamond

* Get the total amount on all orders for each user.

## Contributions

If you see a problem or a typo, please fork, make the necessary changes, and create a pull request so we can review your changes and merge them into the master repo and branch.

## Copyright

© DevMountain LLC, 2017. Unauthorized use and/or duplication of this material without express and written permission from DevMountain, LLC is strictly prohibited. Excerpts and links may be used, provided that full and clear credit is given to DevMountain with appropriate and specific direction to the original content.

<p align="center">
<img src="https://s3.amazonaws.com/devmountain/readme-logo.png" width="250">
</p>
