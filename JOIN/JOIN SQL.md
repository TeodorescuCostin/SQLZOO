# JOIN SQL exercises

1. Modify it to show the matchid and player name for all goals scored by Germany. To identify German players, check for: teamid = 'GER'.

```sql
SELECT matchid, player FROM goal 
WHERE teamid = 'GER'
```

2. From the previous query you can see that Lars Bender's scored a goal in game 1012. Now we want to know what teams were playing in that match. Notice in the that the column matchid in the goal table corresponds to the id column in the game table. We can look up information about game 1012 by finding that row in the game table. Show id, stadium, team1, team2 for just game 1012

```sql
SELECT id, stadium, team1, team2
FROM game
WHERE id = 1012
```

3. Modify it to show the player, teamid, stadium and mdate for every German goal.

```sql
SELECT goal.player, goal.teamid, game.stadium, game.mdate
FROM game
JOIN goal ON game.id = goal.matchid
WHERE goal.teamid = 'GER'
```

4. Show the team1, team2 and player for every goal scored by a player called Mario **player LIKE 'Mario%'**.

```sql
SELECT game.team1, game.team2, goal.player
FROM game
JOIN goal ON game.id = goal.matchid
WHERE player LIKE 'Mario%'
```

5. Show **player, teamid, coach, gtime** for all goals scored in the first 10 minutes **gtime<=10**.

```sql
SELECT goal.player, goal.teamid, eteam.coach, goal.gtime
FROM goal 
JOIN eteam ON goal.teamid = eteam.id
WHERE gtime <= 10
```

6. List the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach. 

```sql
SELECT game.mdate, eteam.teamname
FROM eteam
JOIN game ON game.team1 = eteam.id
WHERE eteam.coach = 'Fernando Santos'
```

7. List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'.

```sql
SELECT goal.player
FROM goal
JOIN game ON goal.matchid = game.id
WHERE stadium = 'National Stadium, Warsaw'
```

8. The example query shows all goals scored in the Germany-Greece quarterfinal. **Instead show the name of all players who scored a goal against Germany.**

```sql
SELECT DISTINCT goal.player
FROM game
JOIN goal ON game.id = goal.matchid
WHERE (game.team1='GER' OR game.team2='GER')
AND goal.teamid <> 'GER'
```

9. Show teamname and the total number of goals scored.

```sql
SELECT eteam.teamname, COUNT(goal.gtime)
FROM eteam
JOIN goal ON eteam.id = goal.teamid
GROUP BY eteam.teamname
```

10. Show the stadium and the number of goals scored in each stadium.

```sql
SELECT game.stadium, COUNT(goal.gtime)
FROM game
JOIN goal ON game.id = goal.matchid
GROUP BY game.stadium
```

11. For every match involving 'POL', show the matchid, date and the number of goals scored.

**The clean version**

```sql
SELECT goal.matchid, game.mdate, COUNT(goal.gtime)
FROM game
JOIN goal ON game.id = goal.matchid
WHERE (team1 = 'POL' OR team2 = 'POL')
GROUP BY goal.matchid, game.mdate
```

**OR**

```sql
SELECT goal.matchid, MAX(game.mdate), COUNT(goal.gtime)
FROM game
JOIN goal ON game.id = goal.matchid
WHERE (team1 = 'POL' OR team2 = 'POL')
GROUP BY goal.matchid
```

12. For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'.

```sql
SELECT goal.matchid, game.mdate, COUNT(goal.gtime)
FROM game
JOIN goal ON game.id = goal.matchid
WHERE teamid = 'GER'
GROUP BY goal.matchid, game.mdate 
```

13. List every match with the goals scored by each team as shown. This will use **"CASE WHEN"** which has not been explained in any previous exercises.

| mdate         | team1 | score1 | team2 | score2 |
|---------------|-------|--------|-------|--------|
| 1 July 2012   | ESP   | 4      | ITA   | 0      |
| 10 June 2012  | ESP   | 1      | ITA   | 1      |
| 10 June 2012  | IRL   | 1      | CRO   | 3      |

Sort your result by mdate, matchid, team1 and team2.

```sql
SELECT mdate,
team1, SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
team2, SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game
LEFT JOIN goal ON matchid = id
GROUP BY mdate, matchid, team1, team2
ORDER BY mdate, matchid, team1, team2
```