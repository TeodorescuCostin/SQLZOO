# SELECT in SELECT SQL exercises


1. List each country name where the population is larger than that of 'Russia'. 

```sql
SELECT name FROM world
WHERE population >
     (SELECT population FROM world
      WHERE name='Russia')
```

2. Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.

```sql
SELECT name
FROM world
WHERE gdp/population >
      (SELECT gdp/population
       FROM world
       WHERE name = 'United Kingdom')
AND continent = 'Europe'
```

3. List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.

```sql
SELECT name, continent
FROM world
WHERE continent IN 
      (SELECT continent
       FROM world
       WHERE name IN ('Argentina', 'Australia'))
```

4. Which country has a population that is more than United Kingdom but less than Germany? Show the name and the population.

```sql
SELECT name, population 
FROM world
WHERE population >
      (SELECT population
       FROM world
       WHERE name = 'United Kingdom')
AND population <
      (SELECT population
       FROM world
       WHERE name = 'Germany')
```

5. Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.

```sql
SELECT name,
       CONCAT(ROUND(
            100*population/
            (SELECT population
             FROM world
             WHERE name = 'Germany')
       ), '%')
FROM world
WHERE continent = 'Europe'
```

6. Which countries have a GDP greater than every country in Europe? [Give the name only.] (Some countries may have NULL gdp values) .

```sql
SELECT name
FROM world
WHERE gdp > (SELECT MAX(gdp)
             FROM world
             WHERE continent = 'Europe')
```

7. Find the largest country (by area) in each continent, show the continent, the name and the area.

>Here I've managed to do two ways one that uses ROW_NUMBER() in case you would want to do it that way and another that does it in a simple way.

**ROW_NUMBER()**

```sql
SELECT continent, name, area
FROM (
  SELECT continent, name, area,
  ROW_NUMBER() OVER (PARTITION BY continent ORDER BY area DESC) AS rn
  FROM world
) AS c
WHERE rn = 1
```

**SIMPLE**

```sql
SELECT continent, name, area
FROM world w1
WHERE area = (
  SELECT MAX(area)
  FROM world w2
  WHERE w1.continent = w2.continent
)
```

8. List each continent and the name of the country that comes first alphabetically.

>Here I've managed to do two ways one that uses ROW_NUMBER() in case you would want to do it that way and another that does it in a simple way.

**ROW_NUMBER()**

```sql
SELECT continent, name
FROM (
      SELECT continent, name,
      ROW_NUMBER() OVER (PARTITION BY continent ORDER BY name
      ASC) AS rn
      FROM world
     ) AS c
WHERE rn = 1
```

**SIMPLE**

```sql
SELECT continent, MIN(name)
FROM world
GROUP BY continent
```

9. Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show name, continent and population.

```sql
SELECT name, continent, population
FROM world w1
WHERE (SELECT MAX(population)
       FROM world w2
       WHERE w1.continent = w2.continent
       GROUP BY continent) <= 25000000
```

**OR**

```sql
SELECT name, continent, population
FROM world
WHERE continent IN (
  SELECT continent
  FROM world
  GROUP BY continent
  HAVING MAX(population) <= 25000000
)
```

10. Some countries have populations more than three times that of all of their neighbours (in the same continent). Give the countries and continents.

>Here I recive an error from the website or whatever I introduce as an answer...