CREATE TABLE salespeople(
    snum INT NOT NULL, 
    sname TEXT, 
    city TEXT, 
    comm INT,
    PRIMARY KEY (snum)
    );

INSERT INTO salespeople(snum, sname, city, comm) 
    VALUES 
    (1001, 'Peel', 'London', .12),
    (1002, 'Serres', 'San Jose', .13),
    (1004, 'Motika', 'London', .11),
    (1007, 'Rafkin', 'Barcelona', .15),
    (1003, 'Alelrod', 'New York', .1);
    
    
CREATE TABLE cust(
    cnum INTEGER, 
    cname TEXT, 
    city TEXT, 
    rating INTEGER,
    snum INTEGER, 
    FOREIGN KEY (snum) REFERENCES salespeople(snum)
    );

INSERT INTO cust(cnum, cname, city, rating, snum) 
    VALUES 
    (2001, 'Hoffman', 'London', 100, 1001),
    (2002, 'Giovanne', 'Rome', 200, 1003),
    (2003, 'Liu', 'San Jose', 300, 1002),
    (2004, 'Grass', 'Berlin', 100, 1002),
    (2006, 'Clemens', 'London', 300, 1007),
    (2008, 'Cisneros', 'San Jose', 300, 1007),
    (2007, 'Pereira', 'Rome', 100, 1004);
  

CREATE TABLE orders(
    onum INT, 
    amt REAL, 
    odate DATE, 
    cnum INT, 
    snum INT,
    FOREIGN KEY (snum) REFERENCES salespeople(snum),
    FOREIGN KEY (cnum) REFERENCES cust(cnum)
    );
    
INSERT INTO orders(onum, amt, odate, cnum, snum) VALUES
    (3001, 18.69, '03-OCT-94', 2008, 1007),
    (3003, 767.19, '03-OCT-94', 2001, 1001),
    (3002, 1900.10, '03-OCT-94', 2007, 1004),
    (3005, 5160.45, '03-OCT-94', 2003, 1002),
    (3006, 1098.16, '04-OCT-94', 2008, 1007),
    (3009, 1713.23, '04-OCT-94', 2002, 1003),
    (3007, 75.75, '05-OCT-94', 2004, 1002),
    (3008, 4723.00, '05-OCT-94', 2006, 1001),
    (3010, 1309.95, '06-OCT-94', 2004, 1002),
    (3011, 9891.88, '06-OCT-94', 2006, 1001);
    
/* Question 1 
    Display snum,sname,city and comm of all salespeople. */
SELECT * FROM salespeople;

/* Question 2 
    Display all snum without duplicates from all orders. */
SELECT DISTINCT snum FROM orders;

/* Question 3 
    Display names and commissions of all salespeople in London. */
SELECT sname, comm FROM salespeople WHERE city = 'London';

/* Question 4 
    All customers with rating of 100. */
SELECT cname FROM cust WHERE rating = 100;

/* Question 5 
    Produce order number, amount and date form all rows in the order table. */
SELECT onum, amt, odate FROM orders;

/* Question 6
    All customers in San Jose, who have rating more than 200. */
SELECT * FROM cust WHERE rating > 200 AND city = 'San Jose';

/* Question 7	
    All customers who were either located in San Jose or had a rating above 200. */
SELECT * FROM cust WHERE rating > 200 OR city = 'San Jose';

/* Question 8 
    All orders for more than $1000. */
SELECT * FROM orders WHERE amt > 1000;

/* Question 9 
    Names and of all salespeople in London with commission above 0.10. */
SELECT sname FROM salespeople WHERE city = 'London' AND comm > .10;

/* Question 10 
    All customers excluding those with rating <= 100 unless they are located in Rome. */
SELECT * FROM cust WHERE rating > 100 OR city = 'Rome';

/* Question 11 
    All salespeople either in Barcelona or in London. */
SELECT * FROM salespeople WHERE city = 'Barcelona' OR city = 'London';

/* Question 12 
    All salespeople with commission between 0.10 and 0.12. (Boundary values should be excluded) */
SELECT * FROM salespeople WHERE comm = 0.11;

/* Question 13 
    All customers with NULL values in city column. */
SELECT * FROM cust WHERE city = NULL;

/* Question 14 
    All orders taken on Oct 3rd and Oct 4th, 1994. */
SELECT * FROM orders WHERE odate BETWEEN '03-OCT-94' AND '04-OCT-94';

/* Question 15 
    All customers serviced by Peel or Motika. */
SELECT * FROM cust WHERE snum = 1001 OR snum = 1004;

/* Question 16 
    All customers whose names begin with a letter from A to B. */
SELECT * FROM cust WHERE cname LIKE 'A%' OR cname LIKE 'B%';

/* Question 17 
    All orders except those with 0 or NULL value in amt field. */
SELECT * FROM orders WHERE amt NOT NULL AND amt > 0;

/* Question 18 
    Count the number of salespeople currently listing orders in the order table. */
SELECT COUNT(DISTINCT snum) FROM orders;

/* Question 19 
 	Largest order taken by each salesperson, datewise. */
SELECT snum, odate, MAX(amt) FROM orders GROUP BY odate, snum ORDER BY odate, snum;

/* Question 20 
 	Largest order taken by each salesperson with order value more than $3000. */
SELECT onum, MAX(amt), odate, cnum, snum FROM orders GROUP BY snum;
 	
 	
/*  Question 21
    Which day had the highest total amount ordered.*/
SELECT odate, SUM(amt) AS total_amt FROM orders WHERE (SELECT amt = MAX(amt) FROM orders);

/* Question 22
    Count all orders for Oct 3rd. */
SELECT COUNT(*) FROM orders WHERE odate = '03-OCT-94';
    
    
/* Question 23
 	Count the number of different non-NULL city values in customers table. */
SELECT COUNT(DISTINCT city) FROM cust WHERE city NOT NULL;

/* Question 24
 	Select each customer’s smallest order. */
SELECT orders.cnum, MIN(orders.amt), cust.cname AS cust_name FROM orders 
    LEFT JOIN cust WHERE orders.cnum = cust.cnum
    GROUP BY orders.cnum;

/* Question 25
 	First customer in alphabetical order whose name begins with G.*/
SELECT cname FROM cust WHERE cname LIKE "G%" ORDER BY cname DESC;
 	
 	
/* Question 26
    Get the output like “ For dd/mm/yy there are ___ orders. */
SELECT 'For 03/10/94 there are ' + CAST(COUNT(onum) AS TEXT) + ' orders.' FROM orders WHERE odate = '03-OCT-94';

/* Question 27
    Assume that each salesperson has a 12% commission. Produce order no., salesperson no., and amount of salesperson’s 
    commission for that order. */
SELECT onum, snum, ROUND(amt * 0.12,2) AS order_comm FROM orders;

/* Question 28
    Find highest rating in each city. Put the output in this form. For the city (city), the highest rating is : (rating). */
SELECT 'For ' + city + ' the highest rating is : ' + MAX(rating) FROM cust GROUP BY city;

/* Question 29
    Display the totals of orders for each day and place the results in descending order.*/
SELECT ROUND(SUM(amt),2) AS day_total, odate FROM orders GROUP BY odate ORDER BY day_total DESC;

/* Question 30
 	All combinations of salespeople and customers who shared a city. (ie same city). */
SELECT cust.cname, cust.city, salespeople.sname, salespeople.city FROM cust 
    JOIN salespeople ON cust.city = salespeople.city ORDER BY cust.city, sname;

/* Question 31 	
    All of all customers matched with the salespeople serving them. */
SELECT * FROM cust JOIN salespeople ON salespeople.snum = cust.snum; 

/* Question 32
 	List each order number followed by the name of the customer who made the order. */
SELECT orders.onum, cust.cname FROM orders JOIN cust ON orders.cnum = cust.cnum;

/* Question 33
 	Names of salesperson and customer for each order after the order number. */
SELECT orders.onum, salespeople.sname, cust.cname FROM orders 
    JOIN cust ON orders.cnum = cust.cnum JOIN salespeople ON orders.snum = salespeople.snum;

/* Question 34
 	Produce all customer serviced by salespeople with a commission above 12%. */
SELECT cust.cnum, cust.cnum, salespeople.comm FROM cust JOIN salespeople ON cust.snum = salespeople.snum AND salespeople.comm > 0.12;

/* Question 35
 	Calculate the amount of the salesperson’s commission on each order with a rating above 100. */
SELECT orders.onum, ROUND(SUM(orders.amt*salespeople.comm),2) AS order_comm, salespeople.sname, orders.cnum, cust.cname FROM orders
   JOIN salespeople ON orders.snum = salespeople.snum 
   JOIN cust ON orders.cnum = cust.cnum AND cust.rating > 100 GROUP BY onum ORDER BY cust.cnum;

/* Question 36
 	Find all pairs of customers having the same rating. */
SELECT a.rating, a.cname, b.cname FROM cust a JOIN cust b 
    WHERE a.rating = b.rating AND a.cname != b.cname ORDER BY a.rating, a.cname, b.cname;

/* Question 37
 	Find all pairs of customers having the same rating, each pair coming once only. */
SELECT a.rating, a.cname, b.cname FROM cust a JOIN cust b 
    WHERE a.rating = b.rating AND a.cname != b.cname AND a.cnum < b.cnum ORDER BY a.rating, a.cname, b.cname;
 
/* Question 38
 	Policy is to assign three salesperson to each customers. Display all such combinations. */


/* Question 39
 	Display all customers located in cities where salesman serres has customer. */

/* Question 40
 	Find all pairs of customers served by single salesperson. */

/* Question 41
 	Produce all pairs of salespeople which are living in the same city. Exclude combinations of salespeople 
 	with themselves as well as duplicates with the order reversed. */


/* Question 42 	
    Produce all pairs of orders by given customer, names that customers and eliminates duplicates. */

/* Question 43
 	Produce names and cities of all customers with the same rating as Hoffman. */

/* Question 44
 	Extract all the orders of Motika. */

/* Question 45
 	All orders credited to the same salesperson who services Hoffman. */

/* Question 46 
 	All orders that are greater than the average for Oct 4. */

/* Question 47
 	Find average commission of salespeople in london. */

/* Question 48
 	Find all orders attributed to salespeople servicing customers in london. */

/* Question 49
 	Extract commissions of all salespeople servicing customers in London. */

/* Question 50
    Find all customers whose cnum is 1000 above the snum of serres. */
SELECT * FROM cust WHERE cnum > (SELECT (snum + 1000) FROM salespeople WHERE sname = "Serres"); 

/* Question 51
    Count the customers with rating above San Jose’s average. */ 
SELECT * FROM cust WHERE rating > (SELECT AVG(rating) FROM cust WHERE city = 'San Jose');

/* Question 52
    Obtain all orders for the customer named Cisneros. (Assume you don’t know his customer no. (cnum)). */
SELECT * FROM orders WHERE cnum = (SELECT cnum FROM cust WHERE cname = 'Cisneros');
    
/* Question 53	
    Produce the names and rating of all customers who have above average orders.*/
SELECT DISTINCT cust.cname, cust.rating FROM cust JOIN orders ON orders.amt > (SELECT AVG(amt) FROM orders);

/* Question 54
    Find total amount in orders for each salesperson for whom this total is greater than the amount of the 
    largest order in the table. */
SELECT orders.snum, salespeople.sname, SUM(orders.amt) AS total_amt FROM orders 
    JOIN salespeople ON orders.snum = salespeople.snum GROUP BY orders.snum HAVING total_amt > (SELECT MAX(amt) FROM orders);
    
/* Question 55
 	Find all customers with order on 3rd Oct. */
SELECT cname FROM cust a, orders b WHERE b.cnum = a.cnum AND b.odate = '03-OCT-94'; 

/* Question 56
 	Find names and numbers of all salesperson who have more than one customer. */
SELECT snum, sname FROM salespeople WHERE snum IN (SELECT snum FROM cust GROUP BY snum HAVING COUNT(snum) > 1);

/* Question 57
 	Check if the correct salesperson was credited with each sale. */
-- How would we know?

/* Question 58
 	Find all orders with above average amounts for their customers. */
SELECT onum, cnum, amt FROM orders a WHERE amt > (SELECT AVG(amt) FROM orders b WHERE a.cnum = b.cnum GROUP BY cnum);


/* Question 59
    Find the sums of the amounts from order table grouped by date, eliminating all those dates where 
    the sum was not at least 2000 above the maximum amount. */
SELECT onum, odate, ROUND(SUM(amt),2) AS daily_total FROM orders GROUP BY odate HAVING daily_total >= 2000;

/* Question 60
 	Find names and numbers of all customers with ratings equal to the maximum for their city. */
SELECT cname, cnum FROM cust WHERE rating > (SELECT MAX(rating) FROM cust GROUP BY city);

/* Question 61
 	Find all salespeople who have customers in their cities who they don’t service. 
    ( Both way using Join and Correlated subquery.) */
SELECT salespeople.sname, salespeople.city FROM salespeople 
    JOIN cust ON salespeople.city = cust.city AND cust.snum != salespeople.snum GROUP BY salespeople.sname;
-- how to do this with correlated subquery?

/* Question 62
 	Extract cnum,cname and city from customer table if and only if one or more of the customers 
    in the table are located in San Jose. */
SELECT cnum, cname, city FROM cust WHERE 2 <= (SELECT COUNT(city) FROM cust WHERE city = 'San Jose');

/* Question 63
 	Find salespeople no. who have multiple customers. */
SELECT snum, count(*) AS cust_count FROM cust GROUP BY snum HAVING cust_count > 1;

/* Question 64
 	Find salespeople number, name and city who have multiple customers. */
SELECT cust.snum, salespeople.sname, cust.city, count(*) AS cust_count FROM cust JOIN salespeople ON cust.snum = salespeople.snum 
    GROUP BY cust.snum HAVING cust_count > 1;
    
/* Question 65
 	Find salespeople who serve only one customer. */
SELECT cust.snum, salespeople.sname, cust.city, count(*) AS cust_count FROM cust JOIN salespeople ON cust.snum = salespeople.snum 
    GROUP BY cust.snum HAVING cust_count = 1;

/* Question 66
 	Extract rows of all salespeople with more than one current order. */
SELECT salespeople.snum, salespeople.sname, salespeople.city, salespeople.comm, COUNT(orders.snum) AS order_count FROM salespeople 
    JOIN orders ON orders.snum = salespeople.snum GROUP BY orders.snum HAVING order_count > 1;

/* Question 67
 	Find all salespeople who have customers with a rating of 300. (use EXISTS) */
SELECT salespeople.snum, salespeople.sname FROM salespeople
    WHERE EXISTS (SELECT rating FROM cust WHERE rating = 300 AND salespeople.snum = cust.snum) GROUP BY salespeople.snum;

/* Question 68
 	Find all salespeople who have customers with a rating of 300. (use JOIN). */
SELECT salespeople.snum, salespeople.sname, cust.rating FROM salespeople 
    JOIN cust ON cust.snum = salespeople.snum GROUP BY salespeople.snum HAVING cust.rating = 300;

/* Question 69
 	Select all salespeople with customers located in their cities who are not assigned to them. (use EXISTS). */
SELECT snum, sname FROM salespeople
	WHERE EXISTS (SELECT cnum FROM cust WHERE salespeople.city = cust.city AND salespeople.snum != cust.snum);

/* Question 70
 	Extract from customers table every customer assigned the a salesperson who currently has 
    at least one other customer (besides the customer being selected) with orders in order table. */
SELECT a.cnum, MAX(c.cname) FROM orders a, cust c WHERE a.cnum = c.cnum GROUP BY a.cnum, a.snum 
    HAVING COUNT(*) < (SELECT COUNT(*) FROM orders b WHERE a.snum = b.snum) ORDER BY a.cnum;

/* Question 71
 	Find salespeople with customers located in their cities ( using both ANY and IN). */
SELECT sname FROM salespeople WHERE  snum = ANY
	(SELECT snum FROM cust WHERE salespeople.city = cust.city AND salespeople.snum = cust.snum);
	
SELECT sname FROM salespeople WHERE snum IN
    (SELECT snum FROM cust WHERE salespeople.city = cust.city AND salespeople.snum = cust.snum);


/* Question 72
 	Find all salespeople for whom there are customers that follow them in alphabetical order. (Using ANY and EXISTS) */


/* Question 73
 	Select customers who have a greater rating than any customer in rome. */


/* Question 74  
 	Select all orders that had amounts that were greater that atleast one of the orders from Oct 6th. */


/* Question 75  
 	Find all orders with amounts smaller than any amount for a customer in San Jose. (Both using ANY and without ANY) */

/* Question 76
 	Select those customers whose ratings are higher than every customer in Paris. ( Using both ALL and NOT EXISTS). */

/* Question 77
 	Select all customers whose ratings are equal to or greater than ANY of the Seeres. */

/* Question 78  
 	Find all salespeople who have no customers located in their city. ( Both using ANY and ALL) */

/* Question 79  
 	Find all orders for amounts greater than any for the customers in London. */

/* Question 80  
 	Find all salespeople and customers located in london. */

/* Question 81
 	For every salesperson, dates on which highest and lowest orders were brought. */

/* Question 82  
 	List all of the salespeople and indicate those who don’t have customers in their cities as well as those who do have. */

/* Question 83
 	Append strings to the selected fields, indicating weather or not a given salesperson was matched to a customer in his city. */

/* Question 84
 	Create a union of two queries that shows the names, cities and ratings of all customers. Those with a rating of 200 or greater will also have the words ‘High Rating’, while the others will have the words ‘Low Rating’.

/* Question 85
 	Write command that produces the name and number of each salesperson and each customer with more than one current order. Put the result in alphabetical order.

/* Question 86
 	Form a union of three queries. Have the first select the snums of all salespeople in San Jose, then second the cnums of all customers in San Jose and the third the onums of all orders on Oct. 3. Retain duplicates between the last two queries, but eliminates and redundancies between either of them and the first.

/* Question 87
 	Produce all the salesperson in London who had at least one customer there.

/* Question 88
 	Produce all the salesperson in London who did not have customers there.

/* Question 89
 	We want to see salespeople matched to their customers without excluding those salesperson who were not currently assigned to any customers. (User OUTER join and UNION)

/* Question 90
 	Insert into table emp1 empno, sal and deptno from emp table.

/* Question 91
 	Update Salary of all employees by 10%.

/* Question 92  
 	Delete all rows from emp for deptno = 10.

/* Question 93  
 	Select list of all jobs which have an annual average salary greater than that managers.

/* Question 94
 	Select list of all employees who have atleast one other employee reporting to them.

/* Question 95
 	Select all employees with correponding level numbers.

/* Question 96
 	Select average salary for employee at each level.

/* Question 97
 	Display organization chart for only those employee who work under ‘JONES’.

/* Question 98
 	Display organization chart for only those employee who work under ‘JONES’ and ‘BLAKE’.

/* Question 99
 	List information about all the people in the organization above ‘ADAMS’.

/* Question 100
	List all the people who work under ‘BLAKE’ except ‘JAMES’.

/* Question 101
 	List all the people who work under ‘KING’ except all employees working under ‘BLAKE’.
	

/* Question 102
 	List all the people who work under ‘KING’ except ‘ADAMS’ and ‘BLAKE’ and all employees working under ‘BLAKE’.


/* Question 103
 	Select max salarys of deptno 10,20 and 30 in single row.


/* Question 104
 	If supply table has three fields vendor, job, part. Find list of vendor who are supplying all part for given job.
	

/* Question 105 
 	List all pairs of orders having same item and qty. */
