# Data Analysis of NETFLIX's Movies and TV Shows using SQL 

![logo](https://images.app.goo.gl/m9i7LqBM8fRLTPb68)
## Project Overview
This project presents an in-depth analysis of Netflix's movie and TV show catalog using SQL. The primary aim is to derive actionable insights and address key business questions using the dataset. This README outlines the project's goals, business challenges, applied solutions, key insights, and overall conclusions.

## Project Objectives
Examine the distribution between movies and TV shows on the platform.

Determine the most frequently assigned ratings for both content types.

Analyze titles by release year, country of origin, and duration.

Categorize and explore content based on specific patterns and keyword-based conditions.

## 📂 Dataset  
The dataset used in this project is sourced from Kaggle:  
🔗 [Netflix Movies and TV Shows Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

### 🧾 Schema  
```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix (
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```
## 💼 Business Problems and Solutions

### 1. Count the Number of Content Items in Each Genre

```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
       COUNT(show_id) AS counts 
FROM netflix
GROUP BY genre;
```

### 2. Categorize Content Based on 'Kill' or 'Violence' in Description

```sql
WITH new_table AS (
  SELECT *, 
         CASE 
           WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
           ELSE 'Good_content'
         END AS category
  FROM netflix
)
SELECT category, COUNT(*) AS total_content 
FROM new_table
GROUP BY category;
```

### 3. List All Movies Released in a Specific Year (e.g., 2020)

```sql
SELECT title, release_year 
FROM netflix
WHERE type = 'Movie' AND release_year = 2020;
```

### 4. Top 5 Countries with the Most Content on Netflix

```sql
SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country,
       COUNT(show_id)
FROM netflix
GROUP BY new_country
ORDER BY COUNT(show_id) DESC
LIMIT 5;
```

### 5. Identify the Longest Movie

```sql
SELECT type, duration 
FROM netflix
WHERE type = 'Movie' AND duration IS NOT NULL
ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INT) DESC;
```

### 6. Find Content Added in the Last 5 Years

```sql
SELECT *, TO_DATE(date_added, 'Month,DD,YYYY') AS date_add 
FROM netflix
WHERE TO_DATE(date_added, 'Month,DD,YYYY') >= CURRENT_DATE - INTERVAL '5 years'
ORDER BY date_add ASC;
```

### 7. All the Movies Directed by 'Rajiv Chilaka'

```sql
SELECT * 
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
```

### 8. List All TV Shows with More Than 5 Seasons

```sql
SELECT * 
FROM netflix
WHERE type = 'TV Show' AND SPLIT_PART(duration, ' ', 1)::numeric > 5;
```

### 9. Count the Number of Movies vs TV Shows
```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
````

### 10. Find Top 5 Years with Highest Average Content Release in India

```sql
SELECT 
  SPLIT_PART(date_added, ',', 2)::numeric AS year,
  COUNT(*) AS total,
  ROUND(COUNT(*)::numeric / 
        (SELECT COUNT(*) FROM netflix WHERE country ILIKE '%India%') * 100, 2) AS avg
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY year
ORDER BY avg DESC
LIMIT 5;
```

### 11. List All Movies that are Documentaries

```sql
SELECT * 
FROM netflix
WHERE type = 'Movie' AND listed_in ILIKE '%Documentaries%';
```

### 12. All Content Without a Director

```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

### 13. Movies Actor 'Salman Khan' Appeared in During the Last 10 Years

```sql
SELECT * 
FROM netflix
WHERE type = 'Movie' 
  AND casts ILIKE '%Salman Khan%' 
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

### 14. Top 10 Actors Who Have Appeared in the Most Movies Produced in India

```sql
SELECT COUNT(*) AS movie_count,
       UNNEST(STRING_TO_ARRAY(casts, ',')) AS actors
FROM netflix
WHERE type ILIKE '%movie%' 
  AND country ILIKE '%India%'
GROUP BY actors
ORDER BY movie_count DESC
LIMIT 10;
```

### 15. Find the Most Common Rating for Movies and TV Shows

```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```

