# Spotify Data Analysis (SQL)


![Spotify Logo](img.avif)



## Overview

This project analyzes a Spotify dataset using SQL to uncover insights into track performance, artist trends, and user engagement. The data is first structured and normalized, followed by advanced SQL queries—including aggregations, CTEs, and window functions—to extract meaningful patterns. The analysis focuses on key factors such as streaming trends, audio features, and platform-based performance, ultimately demonstrating a robust data processing and analytical workflow.

## Business Insights

* **Singles Over Albums**: Audiences engage more strongly with individual tracks. Shifting to a singles-focused release strategy will maximize algorithmic reach and listener retention.
* **Invest in Official Videos**: High-quality visual content directly drives streaming performance. Increased investment in official music videos will boost brand presence and discoverability.
* **Prioritize Spotify Promotion**: For tracks performing stronger on Spotify than YouTube, redirecting promotional efforts toward Spotify tools like playlist pitching and Discovery Mode will deliver better returns.
* **Double Down on Top Tracks**: Focusing promotion on each artist's top 3 most-viewed tracks ensures resources are directed where audience interest is already proven, maximizing engagement across platforms.


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

Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.



## 🗂 Dataset Information

Table Name: `spotify`



# SQL Queries & Analysis

---

---

## 1. Tracks with More Than 1 Billion Streams

```sql
SELECT title, stream 
FROM spotify
WHERE stream > 1000000000;
```

---

## 2. List All Albums with Their Artists

```sql
SELECT DISTINCT album, artist
FROM spotify
ORDER BY 1;
```

---

## 3. Total Comments for Licensed Tracks

```sql
SELECT SUM(comments) AS total_comments
FROM spotify
WHERE licensed = 'true';
```

---

## 4. Tracks Belonging to Album Type "Single"

```sql
SELECT *
FROM spotify
WHERE album_type = 'single';
```

---

## 5. Total Number of Tracks by Each Artist

```sql
SELECT artist,
       COUNT(track) AS total_tracks
FROM spotify
GROUP BY 1;
```

---

## 6. Average Danceability per Album

```sql
SELECT album,
       AVG(danceability) AS avg_danceability
FROM spotify
GROUP BY 1
ORDER BY 2 DESC;
```

---

## 7. Top 5 Tracks with Highest Energy

```sql
SELECT track,
       MAX(energy_liveness) AS max_energy
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

---

## 8. Tracks with Official Video (Views & Likes)

```sql
SELECT track,
       SUM(views) AS total_views,
       SUM(likes) AS total_likes
FROM spotify
WHERE official_video = 'true'
GROUP BY 1
ORDER BY 2 DESC;
```

---

## 9. Total Views for Each Album

```sql
SELECT album,
       SUM(views) AS total_views
FROM spotify
GROUP BY 1
ORDER BY 2 DESC;
```

---

## 10. Tracks Streamed More on Spotify than YouTube

```sql
SELECT *
FROM (
    SELECT track,
           COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END),0) AS streamed_on_youtube,
           COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END),0) AS streamed_on_spotify
    FROM spotify
    GROUP BY 1
) AS t1
WHERE streamed_on_spotify > streamed_on_youtube
AND streamed_on_youtube <> 0;
```

---

## 11. Top 3 Most Viewed Tracks per Artist (Window Function)

```sql
WITH ranking_artist AS (
    SELECT artist,
           track,
           SUM(views) AS total_views,
           DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) AS rank
    FROM spotify
    GROUP BY 1,2
)
SELECT *
FROM ranking_artist
WHERE rank <= 3;
```

---

## 12. Tracks with Above-Average Liveness

```sql
SELECT track,
       artist,
       liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify);
```

---

## 13. Energy Difference per Album (CTE)

```sql
WITH t1 AS (
    SELECT album,
           MAX(energy) AS highest_energy,
           MIN(energy) AS lowest_energy
    FROM spotify
    GROUP BY 1
)
SELECT album,
       highest_energy - lowest_energy AS energy_difference
FROM t1
ORDER BY 2 DESC;
```


Author - SOHAIL ANSARI





