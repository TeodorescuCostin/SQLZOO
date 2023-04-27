# More JOIN SQL exercises

1. List the films where the yr is 1962 [Show id, title].

```sql
SELECT id, title
FROM movie
WHERE yr=1962
```

2. Give year of 'Citizen Kane'.

```sql
SELECT yr
FROM movie
WHERE title = 'Citizen Kane'
```

3. List all of the Star Trek movies, include the id, title and yr (all of these movies include the words Star Trek in the title). Order results by year. 

```sql
SELECT id, title, yr
FROM movie
WHERE title LIKE '%Star Trek%'
```

4.  What id number does the actor 'Glenn Close' have? 

```sql
SELECT id
FROM actor
WHERE name = 'Glenn Close'
```

5. What is the id of the film 'Casablanca'.

```sql
SELECT id
FROM movie
WHERE title = 'Casablanca'
```

6. Obtain the cast list for 'Casablanca'. Use movieid=11768, (or whatever value you got from the previous question).

```sql
SELECT a.name
FROM actor AS a
JOIN casting AS c ON a.id = c.actorid
JOIN movie AS m ON m.id = c.movieid
WHERE m.id = 27
```

**A method which also searches the title *'Casablanca'***

```sql
SELECT a.name
FROM actor AS a
JOIN casting AS c ON a.id = c.actorid
JOIN movie AS m ON m.id = c.movieid
WHERE m.title = 'Casablanca'
```

7. Obtain the cast list for the film 'Alien'.

```sql
SELECT a.name
FROM actor AS a
JOIN casting AS c ON a.id = c.actorid
JOIN movie AS m ON m.id = c.movieid
WHERE m.title = 'Alien'
```

8. List the films in which 'Harrison Ford' has appeared.

```sql
SELECT m.title
FROM movie AS m
JOIN casting AS c ON m.id = c.movieid
JOIN actor AS a ON c.actorid = a.id
WHERE a.name = 'Harrison Ford'
```

9. List the films where 'Harrison Ford' has appeared - but not in the starring role. [Note: the ord field of casting gives the position of the actor. If ord=1 then this actor is in the starring role] .

```sql
SELECT m.title
FROM movie AS m
JOIN casting AS c ON m.id = c.movieid
JOIN actor AS a ON c.actorid = a.id
WHERE a.name = 'Harrison Ford'
AND ord <> 1
```

10. List the films together with the leading star for all 1962 films. 

```sql
SELECT m.title, a.name
FROM movie AS m
JOIN casting AS c ON m.id = c.movieid
JOIN actor AS a ON c.actorid = a.id
WHERE ord = 1 
AND m.yr = 1962
```

11. Which were the busiest years for 'Rock Hudson', show the year and the number of movies he made each year for any year in which he made more than 2 movies. 

```sql
SELECT yr,COUNT(title) FROM
  movie JOIN casting ON movie.id=movieid
        JOIN actor   ON actorid=actor.id
WHERE name='Rock Hudson'
GROUP BY yr
HAVING COUNT(title) > 1
```

12. List the film title and the leading actor for all of the films 'Julie Andrews' played in. 

```sql
SELECT movie.title, a1.name
FROM movie
JOIN casting casting1 ON movie.id = casting1.movieid
JOIN casting casting2 ON movie.id = casting2.movieid
JOIN actor a1 ON casting1.actorid = a1.id
JOIN actor a2 ON casting2.actorid = a2.id
WHERE a2.name = 'Julie Andrews' AND casting1.ord = 1
```

**OR THEIR EXAMPLE**

```sql
SELECT title, name
FROM movie
JOIN casting ON movieid = movie.id AND ord = 1
JOIN actor ON actorid = actor.id
WHERE movie.id IN (
    SELECT movieid FROM casting
    WHERE actorid IN (
        SELECT id FROM actor
        WHERE name='Julie Andrews'
    )
)
```

13. Obtain a list, in alphabetical order, of actors who've had at least 15 starring roles. 

```sql
SELECT name
FROM actor
JOIN casting ON id = actorid AND ord = 1
GROUP BY name
HAVING SUM(ord) >= 15
ORDER BY name ASC
```

14. List the films released in the year 1978 ordered by the number of actors in the cast, then by title. 

```sql
SELECT title, COUNT(DISTINCT actorid) as num_actors
FROM movie
JOIN casting ON id = movieid
WHERE yr = 1978
GROUP BY title
ORDER BY num_actors DESC, title
```

15. List all the people who have worked with 'Art Garfunkel'. 

```sql
SELECT DISTINCT a2.name 
FROM actor a1
JOIN casting c1 ON c1.actorid = a1.id
JOIN casting c2 ON c2.movieid = c1.movieid
JOIN actor a2 ON c2.actorid = a2.id
WHERE a1.name = 'Art Garfunkel'
AND a2.name <> 'Art Garfunkel'
```