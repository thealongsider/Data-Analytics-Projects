These are my SQL solutions to practice problems on the SQL exercise website sql-ex.com

You can see the database schema here: http://www.sql-ex.com/help/select13.php#db_1



1.Find the model number, speed and hard drive capacity for all the PCs with prices below $500.

~~~
SELECT model,
  speed,
  hd
FROM pc
WHERE price < 500
~~~

2.List all printer makers.

~~~
SELECT DISTINCT maker
FROM product
WHERE type = 'Printer'
~~~

3.Find the model number, RAM and screen size of the laptops with prices over $1000

~~~
SELECT model,
  ram,
  screen
FROM laptop
WHERE price > 1000
~~~

4.Find all records from the Printer table containing data about color printers.

~~~
SELECT * 
FROM printer
WHERE color = 'y'
~~~

5.Find the model number, speed and hard drive capacity of PCs cheaper than $600 having a 12x or a 24x CD drive.

~~~
SELECT model,
  speed,
  hd
FROM pc
WHERE price < 600 AND cd IN ('12x','24x')
~~~

6.For each maker producing laptops with a hard drive capacity of 10 Gb or higher, find the speed of such laptops.

~~~
SELECT DISTINCT product.maker,
  laptop.speed
FROM product
JOIN laptop
  ON product.model = laptop.model
WHERE laptop.hd >= 10
ORDER BY product.maker
~~~

7.Get the models and prices for all commercially available products (of any type) produced by maker B.

~~~
SELECT 
  product.model,
  pc.price AS price
FROM product
JOIN pc
  ON pc.model = product. model
WHERE maker = 'B'
UNION
SELECT 
  product.model,
  laptop.price AS price
FROM product
JOIN laptop
  ON laptop.model = product.model
WHERE maker = 'B'
UNION
SELECT 
  product.model,
  printer.price AS price
FROM product
JOIN printer
  ON printer.model = product.model
WHERE maker = 'B'
~~~

8.Find the makers producing PCs but not laptops.

~~~
SELECT maker
FROM product
WHERE type = 'PC'
GROUP BY maker
EXCEPT
SELECT maker
FROM product
WHERE type ='laptop'
GROUP BY maker
~~~

9.Find the makers of PCs with a processor speed of 450 MHz or more.

~~~
SELECT DISTINCT maker
FROM product
JOIN pc
  ON product.model = pc.model
WHERE speed >= 450
~~~

10.Find the printer models having the highest price.

~~~
SELECT model, 
  price
FROM printer
WHERE price = (SELECT MAX(price) FROM printer)
~~~

11.Find out the average speed of PCs.

~~~
SELECT AVG(speed)
FROM pc
~~~

12.Find out the average speed of the laptops priced over $1000.

~~~
SELECT AVG(speed)
FROM laptop
WHERE price >1000
~~~

13.Find out the average speed of the PCs produced by maker A.

~~~
SELECT AVG(speed)
FROM pc
JOIN product
  ON pc.model = product.model
WHERE maker = 'A'
~~~

14.For the ships in the Ships table that have at least 10 guns, get the class, name, and country.

~~~
SELECT c.class,
  s.name,
  c.country
FROM classes c
JOIN ships s
  ON c.class = s.class
WHERE numGuns >=10
~~~

15.Get hard drive capacities that are identical for two or more PCs.

~~~
SELECT hd
FROM pc 
GROUP BY hd
HAVING COUNT(hd)>=2
~~~

16.Get pairs of PC models with identical speeds and the same RAM capacity. Each resulting pair should be displayed only once, i.e. (i, j) but not (j, i). 

~~~
SELECT DISTINCT p1.model,
  p2.model,
  p1.speed,
  p1.ram
FROM pc p1, pc p2
WHERE p1.speed = p2.speed AND p1.ram = p2.ram AND p1.model > p2.model
~~~

17.Get the laptop models that have a speed smaller than the speed of any PC.

~~~
SELECT
DISTINCT product.type,
  laptop.model,
  laptop.speed
FROM product
JOIN laptop
  ON product.model = laptop.model
WHERE speed < ALL (SELECT speed FROM pc)
~~~

18.Find the makers of the cheapest color printers.

~~~
SELECT DISTINCT maker, 
  price
FROM Product
INNER JOIN printer
  ON product.model = printer.model
  AND color = 'y'
WHERE price = (SELECT MIN(price) FROM printer WHERE color = 'y')
~~~

19.For each maker having models in the Laptop table, find out the average screen size of the laptops he produces. 

~~~
SELECT maker, 
  AVG(screen) AS average_screen_size
FROM product
INNER JOIN laptop
  ON product.model = laptop.model
GROUP BY maker
~~~

20.Find the makers producing at least three distinct models of PCs.

~~~
SELECT maker, 
  COUNT(DISTINCT model) AS number_of_pc_models
FROM product
WHERE type = 'PC'
GROUP BY maker
HAVING COUNT(DISTINCT model) >= 3
~~~

21.Find out the maximum PC price for each maker having models in the PC table. 

~~~
SELECT maker,
  MAX(pc.price) AS max_price
FROM product
INNER JOIN pc
  ON product.model = pc.model
GROUP BY product.maker
~~~

22.For each value of PC speed that exceeds 600 MHz, find out the average price of PCs with identical speeds.

~~~
SELECT speed, 
  AVG(price) AS avg_price
FROM pc
WHERE speed > 600
GROUP BY speed
~~~

23.Get the makers producing both PCs having a speed of 750 MHz or higher and laptops with a speed of 750 MHz or higher. 

~~~
SELECT maker
FROM product
INNER JOIN pc
  ON product.model = pc.model
WHERE speed >= 750
INTERSECT
SELECT maker
FROM product
INNER JOIN laptop
  ON product.model = laptop.model
WHERE speed >= 750
~~~

24.List the models of any type having the highest price of all products present in the database.

~~~
WITH a AS(
SELECT model, price
FROM pc
UNION
SELECT model, price
FROM laptop
UNION
SELECT model, price
FROM printer)

SELECT model
FROM a
WHERE price = (SELECT MAX(price) FROM a)
~~~

25. Find the printer makers also producing PCs with the lowest RAM capacity and the highest processor speed of all PCs having the lowest RAM capacity. 

~~~
SELECT DISTINCT maker
FROM product
JOIN pc
  ON product.model=pc.model
WHERE ram = (SELECT MIN(ram) FROM pc)
AND code IN (SELECT code 
              FROM (SELECT code, 
                           model,
                           speed,
                           ram 
                    FROM pc 
                    WHERE ram = (SELECT MIN(ram) FROM pc)
                    ) a 
              WHERE speed = (SELECT MAX(speed) 
                              FROM pc 
                              WHERE ram = (SELECT MIN(ram) FROM pc)
                             )
              )
AND maker IN (SELECT maker 
              FROM product 
              WHERE type = 'printer')
~~~

26.Find out the average price of PCs and laptops produced by maker A.

~~~
WITH a AS (SELECT price
           FROM pc
           INNER JOIN product
             ON product.model = pc.model
           WHERE maker = 'A'
           UNION ALL
           SELECT price
           FROM laptop
           INNER JOIN product
             ON product.model = laptop.model
           WHERE maker = 'A'
           )
           
SELECT AVG(price) AS average
FROM a
~~~

27.Find out the average hard disk drive capacity of PCs produced by makers who also manufacture printers.

~~~
SELECT maker, 
  AVG(hd)
FROM product
JOIN pc
  ON product.model = pc.model
WHERE maker IN (SELECT maker
                FROM product
                WHERE type='printer')
GROUP BY maker
~~~

28.Using Product table, find out the number of makers who produce only one model.

~~~
SELECT COUNT(maker)
FROM (SELECT maker, 
             COUNT(model) AS ct
      FROM product
      GROUP BY maker) a
WHERE ct = 1
~~~

29.Under the assumption that receipts of money (inc) and payouts (out) are registered not more than once a day for each collection point [i.e. the primary key consists of (point, date)], write a query displaying cash flow data (point, date, income, expense). 

~~~
SELECT 
  CASE 
    WHEN i.point IS NULL THEN o.point 
    ELSE i.point 
  END AS point, 
  CASE 
    WHEN i.date IS NULL THEN o.date 
    ELSE i.date 
  END AS date, 
  inc AS income,
  out AS expense
FROM Income_o i
FULL JOIN Outcome_o o
  ON i.point = o.point
  AND i.date = o.date
~~~

30.Under the assumption that receipts of money (inc) and payouts (out) can be registered any number of times a day for each collection point [i.e. the code column is the primary key], display a table with one corresponding row for each operating date of each collection point.Result set: point, date, total payout per day (out), total money intake per day (inc). Missing values are considered to be NULL.

~~~
SELECT  
  CASE 
    WHEN a.point IS NULL THEN b.point 
    ELSE a.point 
  END AS point, 
  CASE 
    WHEN a.date IS NULL THEN b.date 
    ELSE a.date 
  END AS date, 
  payout, 
  intake
FROM(SELECT point, 
            date, 
            SUM(out) as payout
     FROM outcome
     GROUP BY point,date) a
FULL JOIN (SELECT point, 
           date, 
           SUM(inc) as intake
           FROM income
           GROUP BY point,date) b 
  ON b.date = a.date 
  AND b.point = a.point
~~~

31.For ship classes with a gun caliber of 16 in. or more, display the class and the country.

~~~
SELECT class, 
  country
FROM classes
WHERE bore >= 16
~~~

32.One of the characteristics of a ship is one-half the cube of the calibre of its main guns (mw). Determine the average ship mw with an accuracy of two decimal places for each country having ships in the database.

~~~
SELECT country, 
  CAST(AVG(POWER(bore,3.0)/2.0) AS NUMERIC(6,2))
FROM (SELECT country, 
             bore, 
             name
      FROM classes c
      JOIN ships s
        ON s.class = c.class
      UNION
      SELECT country, 
             bore, 
             ship
      FROM classes c
      JOIN outcomes o
        ON o.ship = c.class) a
GROUP BY country
~~~

33.Get the ships sunk in the North Atlantic battle.

~~~
SELECT ship
FROM outcomes
WHERE battle = 'North Atlantic'
  AND result = 'sunk'
~~~

34.In accordance with the Washington Naval Treaty concluded in the beginning of 1922, it was prohibited to build battle ships with a displacement of more than 35 thousand tons. Get the ships violating this treaty (only consider ships for which the year of launch is known). List the names of the ships.

~~~
SELECT DISTINCT name
FROM ships s
FULL JOIN classes c
  ON c.class = s.class
WHERE launched IS NOT NULL
  AND type = 'bb'
  AND launched >= 1922
  AND displacement >35000
~~~

35.Find models in the Product table consisting either of digits only or Latin letters (A-Z, case insensitive) only.

~~~
SELECT model, 
  type
FROM product
WHERE model NOT LIKE '%[^a-z]%'
  OR model NOT LIKE '%[^A-Z]%'
  OR model NOT LIKE '%[^0-9]%'
~~~

36.List the names of lead ships in the database (including the Outcomes table).

~~~
SELECT DISTINCT name
FROM (SELECT name
      FROM ships
      WHERE name=class
      UNION
      SELECT ship AS name
      FROM outcomes
      WHERE ship IN (SELECT class FROM classes)
      ) a
~~~

37.Find classes for which only one ship exists in the database (including the Outcomes table).

~~~
SELECT * FROM (SELECT class 
               FROM (SELECT DISTINCT name, 
                            class
                     FROM ships) b
               UNION ALL
               SELECT DISTINCT class
               FROM classes
               JOIN outcomes
                ON classes.class=outcomes.ship
               WHERE outcomes.ship NOT IN (SELECT name
                                           FROM ships)
               ) a
GROUP BY class
HAVING COUNT(class)=1
~~~

38.Find countries that ever had classes of both battleships (‘bb’) and cruisers (‘bc’).

~~~
SELECT country
FROM classes
WHERE type = 'bb'
INTERSECT
SELECT country 
FROM classes
WHERE type='bc'
~~~

39.Find the ships that `survived for future battles`; that is, after being damaged in a battle, they participated in another one, which occurred later.

~~~
SELECT DISTINCT a.ship 
FROM (SELECT ship,
             result,
             date
      FROM outcomes
      JOIN battles
        ON outcomes.battle=battles.name
      WHERE result = 'damaged') a
JOIN (SELECT ship, 
             result, 
             date
      FROM outcomes
      JOIN battles
        ON outcomes.battle=battles.name) b
ON a.ship=b.ship
AND b.date>a.date
~~~

40.Get the makers who produce only one product type and more than one model. 

~~~
SELECT DISTINCT product.maker,
  product.type
FROM product
JOIN (SELECT maker
      FROM PRODUCT
      GROUP BY maker
      HAVING COUNT(DISTINCT type) = 1
      AND COUNT(DISTINCT model)>1) a
ON a.maker = product.maker
~~~

41.For each maker who has models at least in one of the tables PC, Laptop, or Printer, determine the maximum price for his products. Output: maker; if there are NULL values among the prices for the products of a given maker, display NULL for this maker, otherwise, the maximum price.

~~~
SELECT maker,
  CASE 
    WHEN sum(CASE 
                WHEN price IS NULL THEN 1 
                ELSE 0 
             END) > 0 THEN NULL
    ELSE max(price) 
  END AS price
FROM (SELECT maker, 
             price
      FROM product
      JOIN pc
        ON product.model = pc.model
      UNION ALL
      SELECT maker, 
             price
      FROM product
      JOIN laptop
        ON product.model = laptop.model 
      UNION ALL
      SELECT maker, 
             price
      FROM product
      JOIN printer
        ON product.model = printer.model
      ) a 
GROUP BY maker
~~~

42.Find the names of ships sunk at battles, along with the names of the corresponding battles.

~~~
SELECT ship, 
  battle
FROM outcomes
WHERE result = 'sunk'
~~~

43.Get the battles that occurred in years when no ships were launched into water.

~~~
SELECT name
FROM battles
WHERE DATEPART(year,CAST(date AS date)) NOT IN (SELECT launched 
                                                FROM ships 
                                                WHERE launched IS NOT NULL)
~~~

44.Find all ship names beginning with the letter R.

~~~
SELECT DISTINCT name
FROM (SELECT ship AS name
      FROM outcomes
      UNION
      SELECT name
      FROM ships) a
WHERE name LIKE 'R%'
~~~

45.Find all ship names consisting of three or more words (e.g., King George V). Consider the words in ship names to be separated by single spaces, and the ship names to have no leading or trailing spaces.

~~~
SELECT DISTINCT name
FROM (SELECT ship AS name
      FROM outcomes
      UNION
      SELECT name
      FROM ships) a
WHERE name LIKE '% % %'
~~~

46.For each ship that participated in the Battle of Guadalcanal, get its name, displacement, and the number of guns.

~~~
SELECT DISTINCT ship, 
       displacement, 
       numGuns
FROM classes c
LEFT JOIN ships s
  ON c.class = s.class
RIGHT JOIN outcomes o 
  ON c.class = o.ship
  OR s.name = o.ship
WHERE battle = 'Guadalcanal'
~~~

47.Find the countries that have lost all their ships in battles.

~~~
WITH boat_count AS (SELECT country, COUNT(*) AS scount
                    FROM (SELECT s.name AS name, 
                                 c.country AS country
                          FROM classes c
                          JOIN ships s 
                            ON c.class = s.class
                          UNION
                          SELECT o.ship AS name, 
                                 c.country AS country
                          FROM classes c
                          JOIN outcomes o
                            ON o.ship = c.class
                          ) f
                   GROUP BY country)

SELECT b.country
FROM (SELECT country, 
             COUNT(*) AS sunk_count
      FROM (SELECT s.name AS name, 
                   c.country AS country
            FROM classes c
            JOIN ships s 
              ON c.class = s.class
            UNION
            SELECT o.ship AS name, 
                   c.country AS country
            FROM classes c
            JOIN outcomes o
              ON o.ship = c.class
            ) a
     JOIN outcomes o
      ON o.ship = a.name
     WHERE result ='sunk'
     GROUP BY country
     ) b
JOIN boat_count
  ON boat_count.country = b.country
  AND boat_count.scount = b.sunk_count
~~~

48.Find the ship classes having at least one ship sunk in battles.

~~~
SELECT class
FROM classes
WHERE class IN (SELECT class 
                FROM (SELECT s.name AS ship,
                             c.class AS class
                      FROM classes c
                      JOIN ships s 
                        ON c.class = s.class
                      UNION
                      SELECT o.ship AS ship,
                             c.class AS class
                      FROM classes c
                      JOIN outcomes o
                        ON o.ship = c.class
                     ) a
               JOIN outcomes op
                ON op.ship = a.ship
               WHERE op.result = 'sunk')
~~~

49.Find the names of the ships having a gun caliber of 16 inches (including ships in the Outcomes table).

~~~
SELECT name
FROM (SELECT s.name AS name, 
             c.bore
      FROM ships s
      JOIN classes c
        ON s.class = c.class
      UNION
      SELECT o.ship AS name, 
             c.bore
      FROM outcomes o
      JOIN classes c
        ON c.class = o.ship
      ) a
WHERE a.bore = '16'
~~~

50.Find the battles in which Kongo-class ships from the Ships table were engaged.

~~~
SELECT DISTINCT battle
FROM outcomes
WHERE ship IN (SELECT name
               FROM ships s
               JOIN classes c
                ON s.class = c.class
               WHERE c.class = 'kongo')
~~~

51.Find the names of the ships with the largest number of guns among all ships having the same displacement (including ships in the Outcomes table).

~~~
WITH a AS (SELECT name AS ship, 
                  numGuns, 
                  displacement
           FROM classes c
           JOIN ships s
            ON s.class = c.class
           UNION
           SELECT ship AS ship, 
                  numGuns, 
                  displacement
           FROM outcomes o
           JOIN classes c
           ON o.ship = c.class)

SELECT ship
FROM a
JOIN (SELECT MAX(numGuns) AS MaxNum, 
             displacement
      FROM a
      GROUP BY displacement) AS b
  ON a.displacement = b.displacement
  AND a.numGuns = b.MaxNum
~~~

52.Determine the names of all ships in the Ships table that can be a Japanese battleship having at least nine main guns with a caliber of less than 19 inches and a displacement of not more than 65 000 tons.

~~~
SELECT DISTINCT name
FROM ships s
JOIN classes c
ON s.class = c.class
WHERE (country = 'Japan' OR country IS NULL)
  AND (type ='bb' OR type IS NULL)
  AND (numGuns >=9 OR numGuns IS NULL)
  AND (bore <19 OR bore IS NULL)
  AND (displacement <=65000 OR displacement IS NULL)
~~~

53.With a precision of two decimal places, determine the average number of guns for the battleship classes.

~~~
SELECT CAST(AVG(numGuns*1.0) AS NUMERIC(6,2))
FROM classes
WHERE type = 'bb'
~~~

54.With a precision of two decimal places, determine the average number of guns for all battleships (including the ones in the Outcomes table).

~~~
WITH a AS (SELECT name AS ship, 
                  numGuns
           FROM classes c
           JOIN ships s
            ON s.class = c.class
           WHERE c.type = 'bb'
           UNION
           SELECT ship AS ship, 
                  numGuns
           FROM outcomes o
           JOIN classes c
           ON o.ship = c.class
           WHERE c.type = 'bb')

SELECT CAST(AVG(numGuns*1.0) AS NUMERIC(6,2))
FROM a
~~~

55.For each class, determine the year the first ship of this class was launched. If the lead ship’s year of launch is not known, get the minimum year of launch for the ships of this class.
*Note that my solution would not be counted as "correct" because sql-ex expects Bismark (and any other potential ships in the outcomes table but not in the ships table) to be NULL for its year of launch, but I assumed that it must have been launched at least by the time it participated in the battle. 

~~~
WITH a AS (SELECT ship AS class, 
                  YEAR(date) AS launched
           FROM outcomes o
           JOIN battles b
            ON o.battle = b.name
           JOIN classes c
            ON c.class = o.ship
           UNION
           SELECT class, 
                  launched
           FROM ships)

SELECT class, 
       MIN(launched)
FROM a
GROUP BY class
~~~
