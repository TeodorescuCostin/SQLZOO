# Self JOIN SQL exercises

1. How many stops are in the database. 

```sql
SELECT COUNT(id)
FROM stops
```

2. Find the id value for the stop 'Craiglockhart'.

```sql
SELECT id
FROM stops
WHERE name = 'Craiglockhart'
```

3. Give the id and the name for the stops on the '4' 'LRT' service. 

```sql
SELECT id, name
FROM stops
JOIN route ON id = stop
WHERE num = '4' AND company = 'LRT'
```

4. The query shown gives the number of routes that visit either London Road (149) or Craiglockhart (53). Run the query and notice the two services that link these stops have a count of 2. Add a HAVING clause to restrict the output to these two routes. 

```sql
SELECT company, num, COUNT(*)
FROM route WHERE stop=149 OR stop=53
GROUP BY company, num
HAVING COUNT(*) = 2;
```

5. Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes. Change the query so that it shows the services from Craiglockhart to London Road. 

```sql
SELECT a.company, a.num, a.stop, b.stop
FROM route a
JOIN route b ON a.company = b.company AND a.num = b.num
WHERE a.stop = 53 AND b.stop = 149
```

6. The query shown is similar to the previous one, however by joining two copies of the stops table we can refer to stops by name rather than by number. Change the query so that the services between 'Craiglockhart' and 'London Road' are shown. If you are tired of these places try 'Fairmilehead' against 'Tollcross'.

```sql
SELECT a.company, a.num, stopa.name, stopb.name
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
  JOIN stops stopa ON (a.stop=stopa.id)
  JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart'
AND stopb.name='London Road'
```

7. Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith').

```sql
SELECT r1.company, r1.num
FROM route r1
JOIN route r2 ON r1.num  = r2.num AND r1.company = r2.company
WHERE r1.stop = 115 AND r2.stop = 137
GROUP BY r1.company, r1.num
```

8. Give a list of the services which connect the stops 'Craiglockhart' and 'Tollcross'.

```sql
SELECT r1.company, r1.num
FROM route r1
JOIN route r2 ON r1.num = r2.num AND r1.company = r2.company
JOIN stops s1 ON r1.stop = s1.id
JOIN stops s2 ON r2.stop = s2.id
WHERE s1.name = 'Craiglockhart'
AND s2.name = 'Tollcross'
```

9. Give a distinct list of the stops which may be reached from 'Craiglockhart' by taking one bus, including 'Craiglockhart' itself, offered by the LRT company. Include the company and bus no. of the relevant services. 

```sql
SELECT s1.name, r1.company, r1.num
FROM stops s1
JOIN route r1 ON r1.stop = s1.id
JOIN route r2 ON r2.num = r1.num AND r1.company = r2.company
JOIN stops s2 ON r2.stop = s2.id
WHERE s2.name = 'Craiglockhart'
GROUP BY s1.name, r1.company, r1.num
```

**OR**

```sql
SELECT s.name, r.company, r.num
FROM stops s
JOIN route r ON s.id = r.stop
WHERE r.num IN (
  SELECT r2.num
  FROM route r2
  JOIN stops s2 ON r2.stop = s2.id
  WHERE s2.name = 'Craiglockhart'
)
AND r.company = 'LRT'
GROUP BY s.name, r.company, r.num
```

>**Explanation:** *The subquery in this example selects all the bus numbers that stop at 'Craiglockhart'. The main query then uses these bus numbers to select all the stops that are reachable from 'Craiglockhart' by taking a single bus from the LRT company.*


10. Find the routes involving two buses that can go from Craiglockhart to Lochend. Show the bus no. and company for the first bus, the name of the stop for the transfer, and the bus no. and company for the second bus.

>For this one I've tried to be as descriptive as possible in the naming of the tables so that you can understan easier.

```sql
SELECT DISTINCT start_route.num, start_route.company, transfer_stop.name, end_route.num, end_route.company
FROM route AS start_route
JOIN route AS start_transfer 
    ON start_route.num = start_transfer.num 
    AND start_route.company = start_transfer.company
JOIN stops AS transfer_stop 
    ON start_transfer.stop = transfer_stop.id
JOIN route AS end_transfer
    ON end_transfer.stop = transfer_stop.id
JOIN route AS end_route
    ON end_route.num = end_transfer.num
    AND end_route.company = end_transfer.company
JOIN stops AS start_stop 
    ON start_route.stop = start_stop.id
JOIN stops AS end_stop 
    ON end_route.stop = end_stop.id
WHERE start_stop.name = 'Craiglockhart' 
  AND end_stop.name = 'Lochend'
```