# MySQL and Sakila Database Practices in DBeaver IDE


SQL Database Analysis with Sakila (Beginner to Intermediate)


In this project, we are going to use MySQL and Sakila database to write and execute queries in different levels.


![sakila](https://github.com/user-attachments/assets/849a0710-c7db-496a-94b0-aa869f69614e)


QUESTION 1: What are the top 5 most rented films?

ANSWER:

```sql
SELECT f.title, COUNT(r.rental_id) AS rental_count 
FROM rental r 
JOIN inventory i ON r.inventory_id = i.inventory_id 
JOIN film f ON i.film_id = f.film_id 
GROUP BY f.title 
ORDER BY rental_count DESC 
LIMIT 5;
```

OUTPUT: 

| **TITLE**              | **RENTAL\_COUNT** |
| ---------------------- | ----------------- |
| Bucket Brotherhood     | 34                |
| Rocketeer Mother       | 33                |
| Forward Temple         | 32                |
| Grit Clockwork         | 32                |
| Juggler Hardly         | 32                |


QUESTION 2: Which category has the highest number of rentals?

ANSWER:

```sql
SELECT c.name, COUNT(r.rental_id) AS rental_count
FROM rental r
JOIN inventory i ON i.inventory_id = r.inventory_id
JOIN film_category fc ON fc.film_id = i.film_id
JOIN category c ON c.category_id = fc.category_id
GROUP BY c.name
ORDER BY rental_count DESC
LIMIT 1;
```

OUTPUT: 

| **NAME**   | **RENTAL\_COUNT** |
| ---------- | ----------------- |
| Sports     | 1179              |


QUESTION 3: How many rentals were made in May 2005?

ANSWER: 

```sql
SELECT DATE_FORMAT(r.rental_date, '%Y-%m') AS rental_month,
       COUNT(*) AS rental_count
FROM rental r
WHERE YEAR(r.rental_date) = 2005 AND MONTH(r.rental_date) = 5
GROUP BY rental_month;
```

OUTPUT: 

| **RENTAL\_MONTH** | **RENTAL\_COUNT** |
| ----------------- | ----------------- |
| 2005-05           | 1156              |


QUESTION 4: List the total number of rentals and total amount paid by each customer, ordered by the total amount paid, for the top 10 customers.

ANSWER: 

```sql
SELECT cr.first_name, cr.last_name,
       COUNT(r.rental_id) AS rental_count, 
       SUM(p.amount) AS payment_amount 
FROM customer cr 
JOIN rental r ON r.customer_id = cr.customer_id 
JOIN payment p ON p.rental_id = r.rental_id 
GROUP BY cr.customer_id, cr.first_name, cr.last_name 
ORDER BY payment_amount DESC 
LIMIT 10;
```

OUTPUT:
| **FIRST\_NAME** | **LAST\_NAME** | **RENTAL\_COUNT** | **PAYMENT\_AMOUNT** |
| --------------- | -------------- | ----------------- | ------------------- |
| Karl            | Seal           | 45                | 221.55              |
| Eleanor         | Hunt           | 46                | 216.54              |
| Clara           | Shaw           | 42                | 195.58              |
| Rhonda          | Kennedy        | 39                | 194.61              |
| Marion          | Snyder         | 39                | 194.61              |
| Tommy           | Collazo        | 38                | 186.62              |
| Wesley          | Bull           | 40                | 177.60              |
| Tim             | Cary           | 39                | 175.61              |
| Marcia          | Dean           | 42                | 175.58              |
| Ana             | Bradley        | 34                | 174.66              |


QUESTION 5: Which city generates the most revenue?

ANSWER: 

```sql
SELECT ct.city, SUM(p.amount) AS payment_amount 
FROM city ct 
JOIN address a ON a.city_id = ct.city_id 
JOIN customer cr ON cr.address_id = a.address_id 
JOIN payment p ON p.customer_id = cr.customer_id 
GROUP BY ct.city 
ORDER BY payment_amount DESC 
LIMIT 1;
```

OUTPUT: 

| **CITY**   | **PAYMENT\_AMOUNT** |
| ---------- | ------------------- |
| Cape Coral | 221.55              |


QUESTION 6: Which store has the lowest number of rentals?

ANSWER:

```sql
SELECT a.address, a.district, COUNT(r.rental_id) AS rental_count 
FROM address a 
JOIN customer cr ON cr.address_id = a.address_id 
JOIN rental r ON r.customer_id = cr.customer_id 
GROUP BY a.address, a.district 
ORDER BY rental_count ASC 
LIMIT 1;
```

OUTPUT: 

| **ADDRESS**     | **DISTRICT**       | **RENTAL\_COUNT** |
| --------------- | ------------------ | ----------------- |
| 1769 Iwaki Lane | Kujawsko-Pomorskie | 12                |


QUESTION 7: Which top 5 actors appear most in films?

ANSWER: 

```sql
SELECT a.first_name, a.last_name, COUNT(fa.actor_id) AS film_played 
FROM actor a 
JOIN film_actor fa ON fa.actor_id = a.actor_id 
JOIN film f ON f.film_id = fa.film_id 
GROUP BY a.first_name, a.last_name 
ORDER BY film_played DESC 
LIMIT 5;
```

OUTPUT:

| **FIRST\_NAME** | **LAST\_NAME** | **FILM\_PLAYED** |
| --------------- | -------------- | ---------------- |
| Susan           | Davis          | 54               |
| Gina            | Degeneres      | 42               |
| Walter          | Torn           | 41               |
| Mary            | Keitel         | 40               |
| Matthew         | Carrey         | 39               |


QUESTION 8: What is the total number of transactions and total revenue handled by each staff member?

ANSWER:

```sql
SELECT sf.first_name, sf.last_name,  
       COUNT(r.rental_id) AS transaction_count,  
       SUM(p.amount) AS payment_amount 
FROM staff sf  
JOIN payment p ON p.staff_id = sf.staff_id  
JOIN rental r ON r.rental_id = p.rental_id 
GROUP BY sf.first_name, sf.last_name 
ORDER BY payment_amount DESC;
```

OUTPUT:

| **FIRST\_NAME** | **LAST\_NAME** | **TRANSACTION\_COUNT** | **PAYMENT\_AMOUNT** |
| --------------- | -------------- | ---------------------- | ------------------- |
| Jon             | Stephens       | 7990                   | 33924.06            |
| Mike            | Hillyer        | 8054                   | 33482.50            |


QUESTION 9: Are there any films that were never returned? 

ANSWER:

```sql
SELECT f.title, r.return_date 
FROM film f 
JOIN inventory i ON i.film_id = f.film_id 
JOIN rental r ON r.inventory_id = i.inventory_id 
WHERE r.return_date IS NULL 
ORDER BY f.title ASC 
LIMIT 10;
```

OUTPUT:

| **TITLE**         | **RETURN\_DATE** |
| ----------------- | ---------------- |
| Academy Dinosaur  |                  |
| Ace Goldfinger    |                  |
| Affair Prejudice  |                  |
| African Egg       |                  |
| Ali Forever       |                  |
| Alone Trip        |                  |
| Amadeus Holy      |                  |
| American Circus   |                  |
| Amistad Midsummer |                  |
| Armageddon Lost   |                  |


QUESTION 10: How many rental transactions lasted more than 7 days?

ANSWER:

```sql
SELECT f.title, DATEDIFF(r.return_date, r.rental_date) AS rental_duration 
FROM rental r 
JOIN inventory i ON r.inventory_id = i.inventory_id 
JOIN film f ON i.film_id = f.film_id 
WHERE r.return_date IS NOT NULL 
AND DATEDIFF(r.return_date, r.rental_date) > 7 
LIMIT 10; 
```

OUTPUT:

| **TITLE**        | **RENTAL\_DURATION** |
| ---------------- | -------------------- |
| Academy Dinosaur | 9                    |
| Academy Dinosaur | 9                    |
| Ace Goldfinger   | 8                    |
| Ace Goldfinger   | 8                    |
| Adaptation Holes | 9                    |
| Affair Prejudice | 9                    |
| Affair Prejudice | 8                    |
| Affair Prejudice | 9                    |
| Affair Prejudice | 8                    |
| African Egg      | 8                    |


QUESTION 11: On which day of the week do customers rent films the most?

ANSWER:

```sql
SELECT DAYNAME(rental_date) AS rental_day, 
       COUNT(*) AS rental_count 
FROM rental 
GROUP BY rental_day 
ORDER BY rental_count DESC 
LIMIT 1;
```

OUTPUT:

| **RENTAL\_DAY** | **RENTAL\_COUNT** |
| --------------- | ----------------- |
| Tuesday         | 2463              |


QUESTION 12: Which films has customer ID 5 rented so far?

ANSWER:

```sql
SELECT cr.first_name, cr.last_name, f.title 
FROM customer cr 
JOIN rental r ON r.customer_id = cr.customer_id 
JOIN inventory i ON i.inventory_id = r.inventory_id 
JOIN film f ON f.film_id = i.film_id 
WHERE cr.customer_id = 5 
ORDER BY f.title ASC 
LIMIT 10;
```

OUTPUT:

| **FIRST\_NAME** | **LAST\_NAME** | **TITLE**         |
| --------------- | -------------- | ----------------- |
| Elizabeth       | Brown          | Agent Truman      |
| Elizabeth       | Brown          | Alaska Phantom    |
| Elizabeth       | Brown          | Alter Victory     |
| Elizabeth       | Brown          | Amistad Midsummer |
| Elizabeth       | Brown          | Arizona Bang      |
| Elizabeth       | Brown          | Betrayed Rear     |
| Elizabeth       | Brown          | Bound Cheaper     |
| Elizabeth       | Brown          | Center Dinosaur   |
| Elizabeth       | Brown          | Cleopatra Devil   |
| Elizabeth       | Brown          | Date Speed        |


QUESTION 13: Which customers have rented the most films from the Drama category?

ANSWER:

```sql
SELECT c.name, cr.first_name, cr.last_name, 
       COUNT(rental_id) AS rental_count 
FROM customer cr 
JOIN rental r ON r.customer_id = cr.customer_id 
JOIN inventory i ON i.inventory_id = r.inventory_id 
JOIN film f ON f.film_id = i.film_id 
JOIN film_category fc ON fc.film_id = f.film_id 
JOIN category c ON c.category_id = fc.category_id 
WHERE c.name = 'Drama' 
GROUP BY cr.first_name, cr.last_name 
ORDER BY rental_count DESC 
LIMIT 10; 
```

OUTPUT:

| **NAME** | **FIRST\_NAME** | **LAST\_NAME** | **RENTAL\_COUNT** |
| -------- | --------------- | -------------- | ----------------- |
| Drama    | Pauline         | Henry          | 7                 |
| Drama    | Justin          | Ngo            | 6                 |
| Drama    | Kyle            | Spurlock       | 6                 |
| Drama    | Irene           | Price          | 6                 |
| Drama    | Nancy           | Thomas         | 6                 |
| Drama    | Nora            | Herrera        | 5                 |
| Drama    | Diane           | Collins        | 5                 |
| Drama    | Gabriel         | Harder         | 5                 |
| Drama    | Shane           | Millard        | 5                 |
| Drama    | Bessie          | Morrison       | 5                 |


QUESTION 14: What were the top 3 most rented film categories in the first three months of 2006?

ANSWER:

```sql
SELECT c.name, DATE_FORMAT(r.rental_date, '%Y-%M') AS year_and_month, 
       COUNT(r.rental_id) AS rental_count 
FROM category c 
JOIN film_category fc ON fc.category_id = c.category_id 
JOIN film f ON f.film_id = fc.film_id 
JOIN inventory i ON i.film_id = f.film_id  
JOIN rental r ON r.inventory_id = i.inventory_id 
WHERE YEAR(r.rental_date) = 2006 AND MONTH(r.rental_date) <= 3 
GROUP BY c.name, year_and_month 
ORDER BY rental_count DESC 
LIMIT 3;
```

OUTPUT:

| **NAME**  | **YEAR\_AND\_MONTH** | **RENTAL\_COUNT** |
| --------- | -------------------- | ----------------- |
| Animation | 06-02                | 21                |
| Action    | 06-02                | 17                |
| Sports    | 06-02                | 15                |


QUESTION 15: Which films have two or more language options?

ANSWER:

```sql
SELECT f.title, f.original_language_id AS language_1, 
       COUNT(l.name) AS language_2 
FROM film f 
JOIN language l ON l.language_id = f.language_id 
WHERE f.original_language_id IS NOT NULL 
GROUP BY f.title, f.original_language_id;
```

OUTPUT:

| **TITLE** | **LANGUAGE\_1** | **LANGUAGE\_2** |
| --------- | --------------- | --------------- |
|           |                 |                 |


QUESTION 16: How many customers who rented a film are currently active? 

ANSWER:

```sql
SELECT COUNT(DISTINCT cr.customer_id) AS active_customers_count 
FROM customer cr 
JOIN rental r ON r.customer_id = cr.customer_id 
WHERE cr.active = 1 
GROUP BY cr.active;
```

OUTPUT:

| **ACTIVE\_CUSTOMERS\_COUNT** |
| ---------------------------- |
| 584                          |


QUESTION 17: Which cities have customers but no store?

ANSWER:

```sql
SELECT DISTINCT ct.city 
FROM city ct 
JOIN address a ON ct.city_id = a.city_id 
JOIN customer cr ON a.address_id = cr.address_id 
WHERE ct.city_id NOT IN ( 
SELECT DISTINCT a.city_id 
FROM address a 
JOIN store s ON a.address_id = s.address_id 
) 
Limit 10; 
```

OUTPUT:

| **CITY**       |
| -------------- |
| Sasebo         |
| San bernardino |
| Athenai        |
| Myingyan       |
| Nantou         |
| Laredo         |
| Kragujevac     |
| Hamilton       |
| Masqat         |
| Esfahan        |


QUESTION 18: What is the average rental duration for each film?

ANSWER:

```sql
SELECT f.title, AVG(DATEDIFF(r.return_date, r.rental_date)) AS rental_duration 
FROM film f 
JOIN inventory i ON i.film_id = f.film_id 
JOIN rental r ON r.inventory_id = i.inventory_id  
GROUP BY f.title 
ORDER BY rental_duration DESC 
LIMIT 10;
```

OUTPUT:

| **TITLE**            | **RENTAL\_DURATION** |
| -------------------- | -------------------- |
| African egg          | 7.0909               |
| Flight lies          | 7.0833               |
| Hardly robbers       | 7.0000               |
| Impact aladdin       | 7.0000               |
| Wagon jaws           | 6.9000               |
| Mother oleander      | 6.8571               |
| Madre gables         | 6.7857               |
| Reds pocus           | 6.7778               |
| Silverado goldfinger | 6.7273               |
| Notorious reunion    | 6.7143               |


QUESTION 19: Which top 3 films have the most late returns?

ANSWER:

```sql
SELECT f.title, MAX(DATEDIFF(r.return_date, r.rental_date)) AS most_rental_duration 
FROM film f 
JOIN inventory i ON i.film_id = f.film_id 
JOIN rental r ON r.inventory_id = i.inventory_id 
GROUP BY f.title 
ORDER BY most_rental_duration DESC 
LIMIT 3;
```

OUTPUT:

| **TITLE**        | **MOST\_RENTAL\_DURATION** |
| ---------------- | -------------------------- |
| Army flintstones | 10                         |
| Alley evolution  | 10                         |
| Armageddon lost  | 10                         |


QUESTION 20: Who is the top-earning staff member?

ANSWER:

```sql
SELECT sf.first_name, sf.last_name,
       SUM(p.amount) AS staff_payment 
FROM staff sf 
JOIN payment p ON p.staff_id = sf.staff_id 
GROUP BY sf.first_name, sf.last_name 
ORDER BY staff_payment DESC 
LIMIT 1;
```

OUTPUT:

| **FIRST\_NAME** | **LAST\_NAME** | **STAFF\_PAYMENT** |
| --------------- | -------------- | ------------------ |
| Jon             | Stephens       | 33924.06           |


These were the questions, answers, and outputs of some MySQL queries. (Some of the "LIMIT" usages were used to shorten the outputs)
