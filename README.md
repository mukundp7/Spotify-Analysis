# Spotify SQL Data Analysis Project

![Spotify Logo](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_logo.jpg)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## Exploratory Data Analysis
1. Which tracks have more than 1 billion streams?
```sql
SELECT DISTINCT track, stream
FROM spotify 
WHERE stream > 1000000000;
```

2. What are all the albums in the dataset along with their respective artists?
```sql
SELECT DISTINCT album, artist
FROM spotify 
ORDER BY artist;
```

3. What are the total number of comments for licenced tracks? (licensed = TRUE)
```sql
SELECT SUM(comments) as total_comments
FROM spotify 
WHERE licensed = true;
```
 
4. Which tracks in the dataset are singles?
```sql
SELECT * 
FROM spotify 
WHERE album_type = 'single';
```
5. What are the total number of tracks for each artist?
```sql
SELECT artist, COUNT(*) as total_tracks 
FROM spotify 
GROUP BY artist
ORDER BY 2;
```

6. What is the average danceability of tracks in each album?
```sql
SELECT album, AVG(danceability) as AVG_danceability 
FROM spotify 
GROUP BY album 
ORDER BY 2 desc;
```

7. What are the top 5 tracks with the highest energy values?
```sql
SELECT DISTINCT track, MAX(energy) 
FROM spotify 
GROUP BY 1
ORDER BY 2 desc
LIMIT 5; 
```

8. What are all the tracks with their views and likes where the video was official? (official_video = TRUE)
```sql
SELECT track, SUM(views) as total_views, SUM(likes) as total_likes
FROM spotify 
WHERE official_video = true 
GROUP BY 1 
ORDER BY 2 DESC;
```

9. What are the total views for each album for all the associated tracks?
```sql
SELECT album, track, SUM(views) as total_views
FROM spotify
GROUP BY 1, 2
ORDER BY 3 DESC;
```

10. What are the track names for tracks that have been streamed on Spotify more than YouTube?
```sql
SELECT * FROM
(SELECT 
	track, 
	COALESCE(SUM(CASE WHEN most_played_on = 'YouTube' THEN stream END),0) as streamed_on_youtube, 
	COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END),0) as streamed_on_spotify 
FROM spotify 
GROUP BY 1
) as t1
WHERE 
	streamed_on_spotify > streamed_on_youtube; 
```

11. What are the top 3 most-viewed tracks for each artist? (Using a window function)
```sql
WITH artist_ranking 
AS
(SELECT 
	artist,
	track,
	SUM(views) as total_views,
	DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) as rank
FROM spotify
GROUP BY 1, 2
ORDER BY 1, 3 DESC
)
SELECT * FROM artist_ranking 
WHERE rank <= 3;
```

12. What are the tracks that have a liveness score is above average? 
```sql
SELECT * FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
```

13. What is the difference between the highest and lowest energy values for tracks in each album? (Calculated using a WITH clause)
```sql
WITH t1
AS
(SELECT
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energy
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	ROUND(CAST(highest_energy - lowest_energy AS numeric), 2) AS energy_diff
FROM t1
ORDER BY 2 DESC
```

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## Next Steps
- **Visualize the Data**: Use a data visualization tool like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.
- **Advanced Querying**: Dive deeper into query optimization and explore the performance of SQL queries on larger datasets.

