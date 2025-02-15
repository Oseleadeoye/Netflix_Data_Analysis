
# Netflix Data Analysis Using SQL

![Netflix logo](https://github.com/Dhruv-404-debug/Netflix_Data_Analysis/blob/main/logo.png)



## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.



## Dataset
The data for this project is sourced from the Kaggle dataset:
https://www.kaggle.com/datasets/shivamb/netflix-shows




## Schema
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
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


## Business Problems and Solutions


### 1. Count the Number of Movies vs TV Shows

SELECT type,
    COUNT(*) AS num_movies_tvshows
FROM netflix_titles 
GROUP BY type;

Objective: Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for Movies and TV Shows


WITH RatingCounts AS (
    SELECT 
        type, 
        rating, 
        COUNT(*) AS count,
        RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking
    FROM netflix_titles
    WHERE rating IS NOT NULL
    GROUP BY type, rating
)
SELECT type, rating, count
FROM RatingCounts
WHERE ranking = 1;


Objective: Identify the most frequently occurring rating for each type of content.

### 3. List All Movies Released in a Specific Year (e.g., 2020)


SELECT * 

FROM netflix_titles

WHERE release_year = 2020;

Objective: Retrieve all movies released in a specific year.

### 4. Find the Top 5 Countries with the Most Content on Netflix

SELECT TOP 5 country, COUNT(*) AS total_content
FROM netflix_titles
WHERE country IS NOT NULL
GROUP BY country
ORDER BY total_content DESC

Objective: Identify the top 5 countries with the highest number of content items.

### 5. Identify the Longest Movie

SELECT TOP 1 title, duration
FROM netflix_titles
ORDER BY duration DESC;

Objective: Find the movie with the longest duration.


### 6. Find Content Added in the Last 5 Years

SELECT title, type, country, date_added
FROM netflix_titles
WHERE date_added IS NOT NULL 
AND date_added >= DATEADD(YEAR, -5, GETDATE())
ORDER BY date_added DESC;


### 7. Find All Movies/TV Shows by Director 'Steven Spielberg'

SELECT title, type
FROM netflix_titles
WHERE director = 'Steven Spielberg'
ORDER BY date_added DESC;

Objective: List all content directed by 'Rajiv Chilaka'.

### 8. List All TV Shows with More Than 5 Seasons

SELECT title, type, country, duration
FROM netflix_titles
WHERE type = 'TV Show'
AND duration LIKE '%Seasons%' 
AND TRY_CAST(SUBSTRING(duration, 1, CHARINDEX(' ', duration) - 1) AS INT) > 5
ORDER BY duration DESC;
  
Objective: Identify TV shows with more than 5 seasons.


### 9. Count the Number of Content Items in Each Genre
SELECT listed_in, COUNT(*) AS content_count
FROM netflix_titles
WHERE listed_in IS NOT NULL
GROUP BY listed_in
ORDER BY content_count DESC;

Objective: Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in Canada on netflix. return top 5 year with highest avg content release!

SELECT TOP 5 release_year AS year, AVG(content_count) AS avg_content_release
FROM (
    SELECT release_year, COUNT(*) AS content_count
    FROM netflix_titles
    WHERE country = 'Canada' AND release_year IS NOT NULL
    GROUP BY release_year
) AS year_content_counts
GROUP BY release_year
ORDER BY avg_content_release DESC;
Objective: Calculate and rank years by the average number of content releases by Canada.

### 11. List All Movies that are Documentaries
SELECT * 
FROM netflix_titles
WHERE listed_in LIKE '%Documentaries';

Objective: Retrieve all movies classified as documentaries.

### 12. Find All Content Without a Director
SELECT * 
FROM netflix_titles
WHERE director IS NULL;

Objective: List content that does not have a director.

### 13. Find How Many Movies Actor 'Ryan Reynolds' Appeared in the Last 10 Years
SELECT COUNT(*) AS movie_count

FROM netflix_titles

WHERE date_added >= DATEADD(YEAR, -10, GETDATE())

  AND type = 'Movie'
  
  AND cast LIKE '%Ryan Reynolds%';
  
Objective: Count the number of movies featuring 'Ryan Reynolds' in the last 10 years.



### 14. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
SELECT 
    CASE 
        WHEN LOWER(title) LIKE '%kill%' OR LOWER(description) LIKE '%kill%' 
             OR LOWER(title) LIKE '%violence%' OR LOWER(description) LIKE '%violence%' THEN 'Bad'
        ELSE 'Good'
    END AS category,
    COUNT(*) AS content_count
FROM netflix_titles
GROUP BY 
    CASE 
        WHEN LOWER(title) LIKE '%kill%' OR LOWER(description) LIKE '%kill%' 
             OR LOWER(title) LIKE '%violence%' OR LOWER(description) LIKE '%violence%' THEN 'Bad'
        ELSE 'Good'
    END;

Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.

Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.

Geographical Insights: The top countries and the average content releases by Canada highlight regional content distribution.

Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.




