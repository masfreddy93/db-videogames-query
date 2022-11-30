## Repo
db-videogames-query

### Schema E-R
![[Pasted image 20221129162923.png]]

### Query
Dopo aver importato il dump del db contenente i dati ([[Videogiochi DB]]), eseguire le seguenti query:
#### SELECT
1. Selezionare tutte le software house americane (3)
```
SELECT *
FROM software_houses
WHERE country LIKE 'United States'
```
2. Selezionare tutti i giocatori della cittÃ  di 'Rogahnland' (2)
```
SELECT *
FROM players
WHERE city LIKE 'Rogahnland'
```
3. Selezionare tutti i giocatori il cui nome finisce per "a" (220)
```
SELECT *
FROM players
WHERE name LIKE '%a'
```
4. Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)
```
SELECT *
FROM reviews
WHERE player_id = 800
```
5. Contare quanti tornei ci sono stati nell'anno 2015 (9)
```
SELECT count(*)
FROM tournaments
WHERE year = 2015
```
6. Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)
```
SELECT *
FROM awards
WHERE description LIKE '%facere%'
```
7. Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)
```
SELECT distinct videogame_id
FROM category_videogame
WHERE category_id = 2 || category_id = 6
```
8. Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)
```
SELECT distinct *
FROM reviews
WHERE rating >= 2 && rating <= 4
LIMIT 10000
```
9. Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)
```
SELECT *
FROM videogames
WHERE YEAR(release_date) = 2020
```
10. Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da stelle, mostrandoli una sola volta (443)
```
SELECT DISTINCT videogame_id
FROM reviews
WHERE rating = 5
```
##### **BONUS**
11. Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)
```
SELECT count(*) AS review_number, SUM(rating) / count(*) AS avg_rating
FROM reviews
WHERE videogame_id = 412
```
12. Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)
```
SELECT *
FROM videogames
WHERE software_house_id = 1 && YEAR(release_date) = 2018
```

---

#### GROUP BY
1. Contare quante software house ci sono per ogni paese (3)
```
SELECT COUNT(*), country
FROM software_houses
GROUP BY country
```
2. Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)
```
SELECT videogame_id, count(*)
FROM reviews
GROUP BY videogame_id
```
3. Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)
```
SELECT pegi_label_id, COUNT(*)
FROM pegi_label_videogame
GROUP BY pegi_label_id
```
4. Mostrare il numero di videogiochi rilasciati ogni anno (11)
```
SELECT YEAR(release_date) AS year_of_release, count(*) AS number_videogames
FROM videogames
GROUP BY year_of_release
```
5. Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)
```
SELECT device_id, COUNT(*)
FROM device_videogame
GROUP BY device_id
```
6. Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)
```
SELECT videogame_id, ROUND(SUM(rating) / COUNT(*), 2) AS avg_rating
FROM reviews
GROUP BY videogame_id
ORDER BY avg_rating DESC
```

---

#### JOIN
1. Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)
```
SELECT distinct players.*
FROM players
	JOIN reviews
		ON players.id = reviews.player_id
WHERE reviews.id IS NOT null
```
2. Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)
```
SELECT distinct videogames.*
FROM videogames
	LEFT JOIN tournament_videogame
		ON videogames.id = tournament_videogame.videogame_id
	LEFT JOIN tournaments
		ON tournament_videogame.tournament_id = tournaments.id
WHERE tournaments.year = 2016
```
3. Mostrare le categorie di ogni videogioco (1718)
```
SELECT categories.name AS categoria, category_videogame.videogame_id 
FROM categories
	JOIN category_videogame
    ON categories.id = category_videogame.category_id
ORDER BY category_videogame.videogame_id
LIMIT 10000
```
4. Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)
```
SELECT distinct software_houses.*
FROM software_houses
	JOIN videogames
		ON software_houses.id = videogames.software_house_id
WHERE YEAR(videogames.release_date) > 2020
```
5. Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)
```
SELECT awards.name, videogames.name AS videogame, software_houses.name AS software_house
FROM awards
	JOIN award_videogame
		ON awards.id = award_videogame.award_id
	JOIN videogames
		ON award_videogame.videogame_id = videogames.id
	JOIN software_houses
		ON videogames.software_house_id = software_houses.id
	ORDER BY software_houses.name
```
6. Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)
```

```
7. Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)
```
SELECT distinct videogames.*
FROM videogames
	JOIN tournament_videogame
		ON videogames.id = tournament_videogame.videogame_id
	JOIN tournaments
		ON tournament_videogame.tournament_id = tournaments.id
	JOIN player_tournament
		ON tournaments.id = player_tournament.tournament_id
	JOIN players
		ON player_tournament.player_id = players.id
WHERE players.name LIKE 's%'
```
8. Selezionare le cittÃ  in cui Ã¨ stato giocato il gioco dell'anno del 2018 (36)
```
SELECT awards.id, awards.name, award_videogame.year, videogames.name,  tournaments.city
FROM tournaments
	JOIN tournament_videogame
		ON tournaments.id = tournament_videogame.tournament_id
	JOIN videogames
		ON videogames.id = tournament_videogame.videogame_id
	JOIN award_videogame
		ON videogames.id = award_videogame.videogame_id
	JOIN awards
		ON awards.id = award_videogame.award_id
WHERE awards.name LIKE 'Gioco dell\'anno' 
	AND award_videogame.year = 2018
```
9. Selezionare i giocatori che hanno giocato al gioco piÃ¹ atteso del 2018 in un torneo del 2019 (3306)
```

```

##### **BONUS**
10. Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)
11. Selezionare i dati del videogame (id, name, release_date, totale recensioni) con piÃ¹ recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)
12. Selezionare la software house che ha vinto piÃ¹ premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)
13. Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 1.5 (10)