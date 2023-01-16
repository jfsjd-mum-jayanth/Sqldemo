# Sqldemo

Q3:
Consider the schema for Movie Database:
ACTOR(Act_id, Act_Name, Act_Gender)
DIRECTOR(Dir_id, Dir_Name, Dir_Phone)
MOVIES(Mov_id, Mov_Title, Mov_Year, Mov_Lang, Dir_id)
MOVIE_CAST(Act_id, Mov_id, Role)
RATING(Mov_id, Rev_Stars)

mysql> create table Actor(act_id integer primary key,act_name varchar(100),act_gender varchar(10));
Query OK, 0 rows affected (0.02 sec)

mysql> create table Director(dir_id integer primary key,dir_name varchar(200),dir_phone varchar(100));
Query OK, 0 rows affected (0.01 sec)

mysql> create table Movies(mov_id integer primary key,mov_title varchar(255),mov_year year,mov_lang varchar(100),dir_id int, foreign key (dir_id) references Director(dir_id));
Query OK, 0 rows affected (0.01 sec)

mysql> create table Movie_cast (act_id int,foreign key (act_id) references Actor(act_id), mov_id int, foreign key(mov_id) references Movies(mov_id),role varchar(100), primary key(act_id,mov_id) );

mysql> create table Rating(mov_id integer primary key , foreign key(mov_id) references Movies(mov_id),rev_stars integer);
Query OK, 0 rows affected (0.01 sec)


mysql> insert into Actor values(1001, 'Tom Crusie','M'),(1002, 'Chris Hemsworth','M'),(1003, 'Angelina Jolie','F'),(1004, 'Margot Robbie','F'),(1005, 'Kate Winslet','F'),(1006, 'Robert Downey','M');
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> insert into Director values
    -> (9001, 'Hitchcock',9874562154),(9002, 'Steven Spielberg',9874560054),(9003, 'Joseph Levitan',9874562178),(9004, 'Christopher Loyd',9874564454),(9005, 'Yash Chopra',9874562994),(9006, 'Tom Jones',9874503154);
Query OK, 6 rows affected (0.00 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> select * from Director;
+--------+------------------+------------+
| dir_id | dir_name         | dir_phone  |
+--------+------------------+------------+
|   9001 | Hitchcock        | 9874562154 |
|   9002 | Steven Spielberg | 9874560054 |
|   9003 | Joseph Levitan   | 9874562178 |
|   9004 | Christopher Loyd | 9874564454 |
|   9005 | Yash Chopra      | 9874562994 |
|   9006 | Tom Jones        | 9874503154 |
+--------+------------------+------------+
6 rows in set (0.00 sec)
mysql> insert into Movies values
    -> (101,'Iron Man',2014,'English',9001),(102,'Prosperity',2001,'Spanish',9001),(103,'Spiderman',1998,'English',9002),(104,'Star Wars',1999,'English',9003),(105,'Thor',2017,'English',9002),(106,'Captain America',1994,'English',9004);
Query OK, 6 rows affected (0.00 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> select * from Movies;
+--------+-----------------+----------+----------+--------+
| mov_id | mov_title       | mov_year | mov_lang | dir_id |
+--------+-----------------+----------+----------+--------+
|    101 | Iron Man        |     2014 | English  |   9001 |
|    102 | Prosperity      |     2001 | Spanish  |   9001 |
|    103 | Spiderman       |     1998 | English  |   9002 |
|    104 | Star Wars       |     1999 | English  |   9003 |
|    105 | Thor            |     2017 | English  |   9002 |
|    106 | Captain America |     1994 | English  |   9004 |
+--------+-----------------+----------+----------+--------+
6 rows in set (0.01 sec)

mysql> insert into Movie_cast values (1001,101,'Joey'),(1001,102,'Conor'),(1002,102,'Tim'),(1003,103,'Kate'),(1004,104,'Claire'),(1006,105,'Sally'),(1005,106,'Jo');
Query OK, 7 rows affected (0.00 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> insert into Movie_cast values(1002,106,'Craft'),(1002,104,'Josh'),(1005,105,'Roy');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0
mysql> select * from Movie_cast;
+--------+--------+--------+
| act_id | mov_id | role   |
+--------+--------+--------+
|   1001 |    101 | Joey   |
|   1001 |    102 | Conor  |
|   1002 |    102 | Tim    |
|   1002 |    104 | Josh   |
|   1002 |    106 | Craft  |
|   1003 |    103 | Kate   |
|   1004 |    104 | Claire |
|   1005 |    105 | Roy    |
|   1005 |    106 | Jo     |
|   1006 |    105 | Sally  |
+--------+--------+--------+

mysql> insert into Rating values(101,4),(102,3),(103,5),(104,2),(105,4),(106,3);
Query OK, 6 rows affected (0.00 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> select * from Rating;
+--------+-----------+
| mov_id | rev_stars |
+--------+-----------+
|    101 |         4 |
|    102 |         3 |
|    103 |         5 |
|    104 |         2 |
|    105 |         4 |
|    106 |         3 |
+--------+-----------+
6 rows in set (0.00 sec)


---------------------------------------------------------------------------------------------------------------------------------------------
QUERIES
-----------------------------------------------------------------------------------------------------------------------------------------------



Q 3.1. List the titles of all movies directed by ‘Hitchcock’.

mysql> select mov_title from Movies where dir_id in (select dir_id from Director where dir_name='Hitchcock');
+------------+
| mov_title  |
+------------+
| Iron Man   |
| Prosperity |
+------------+
2 rows in set (0.00 sec)


Q3.2. Find the movie names where one or more actors acted in two or more movies.

mysql> select distinct m.mov_title,c.act_id from Movies m, Movie_cast c where m.mov_id=c.mov_id and c.act_id in (select act_id from Movie_cast group by act_id having count(mov_id)>1);

+-----------------+--------+
| mov_title       | act_id |
+-----------------+--------+
| Iron Man        |   1001 |
| Prosperity      |   1001 |
| Prosperity      |   1002 |
| Star Wars       |   1002 |
| Thor            |   1005 |
| Captain America |   1002 |
| Captain America |   1005 |
+-----------------+--------+

Q3.3. List all actors who acted in a movie before 2000 and also in a movie after
2015 (use JOIN operation).

mysql> select act_name from Actor where act_id in(select a.act_id from (select act_id from Movie_cast natural join Movies where mov_year<2000)a inner join  (select act_id from Movie_cast natural join Movies where mov_year>2015)b on a.act_id=b.act_id);

+--------------+
| act_name     |
+--------------+
| Kate Winslet |
+--------------+
1 row in set (0.00 sec)

Q3.4. Find the title of movies and number of stars for each movie that has at least
one rating and find the highest number of stars that movie received. Sort the
result by movie title.

mysql> select mov_title,max(rev_stars) from Movies m,Rating r where m.mov_id=r.mov_id group by m.mov_title having count(r.rev_stars)>0;
+-----------------+----------------+
| mov_title       | max(rev_stars) |
+-----------------+----------------+
| Captain America |              3 |
| Iron Man        |              4 |
| Prosperity      |              3 |
| Spiderman       |              5 |
| Star Wars       |              2 |
| Thor            |              4 |
+-----------------+----------------+

Q3.5.  Update rating of all movies directed by ‘Steven Spielberg’ to 5.

mysql> update Rating set rev_stars=5 where mov_id in (select mov_id from Movies inner join Director on Movies.dir_id=Director.dir_id and Director.dir_name='Steven Spielberg');
Query OK, 1 row affected (0.00 sec)
Rows matched: 2  Changed: 1  Warnings: 0

mysql> select * from Rating;
+--------+-----------+
| mov_id | rev_stars |
+--------+-----------+
|    101 |         4 |
|    102 |         3 |
|    103 |         5 |
|    104 |         2 |
|    105 |         5 |
|    106 |         3 |
+--------+-----------+
6 rows in set (0.00 sec)
