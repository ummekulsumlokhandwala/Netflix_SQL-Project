# Netflix Data Analysis Project - SQL
![image](https://github.com/user-attachments/assets/83068fdd-5308-4335-8c51-a5892198f6a8)
# Overview
This project uses SQL to analyze Netflix's movies and TV shows data, aiming to extract insights and address business questions. The README summarizes the project's goals, challenges, solutions, and key findings.
# Dataset
The data for the project is resourced from the Kaggle web library:
[Dataset Link](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)
# Objective
1. Analyze the distribution of content types, comparing movies and TV shows.
2. Identify the most common ratings for both movies and TV shows.
3. List and analyze content according to release years, countries, and durations.
4. Explore and categorize content based on specific criteria and keywords.
# Questions and Solutions
1. Count the number of movies versus TV shows.

   Objective: Find out how different types of content are distributed on Netflix.
```SQL
   #CODE:-
   SELECT type, COUNT(*)
   FROM netflix
   GROUP BY 1;
```
2. Identify the most common ratings for movies and TV shows.

   Objective: Find the most common rating for each type of content.
```SQL
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
3. List all movies released in a specific year (e.g., 2020).

   Objective: Find all movies released in a certain year.
```SQL
SELECT * 
FROM netflix
WHERE release_year = 2020;
```
4. Find the top five countries with the most content available on Netflix.

   Objective: Find the five countries with the most content items.
```SQL
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
 ```
5. Identify the longest movie.

   Objective: Find the movie that has the longest running time.
```SQL
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
 ```
6. Find content added in the last five years.

   Objective: Gather the content that has been added to Netflix in the past five years.
```SQL
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
 ```

7. Retrieve all movies and TV shows directed by Rajiv Chilaka.

   Objective: List all content directed by 'Rajiv Chilaka'.
```SQL
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
  ```
8. List all TV shows that have more than five seasons.

   Objective: Find TV shows that have more than five seasons.
```SQL
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
 ```   
10. Count the number of content items in each genre.

    Objective: Count how many content items belong to each genre.
```SQL
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
  ``` 
11. Find the average number of content releases in India on Netflix for each year, and return the top five years with the highest average.

    Objective: Calculate and rank years by the average number of content releases by India.
```SQL
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
``` 
12. List all movies that are documentaries.

  Objective: Find all movies that are documentaries.
```SQL
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```
# Conclusion
Content Distribution: The dataset features a variety of movies and TV shows across different ratings and genres.

Common Ratings: Analyzing common ratings reveals insights about the target audience.

Geographical Insights: Key countries and India's average content releases highlight regional distribution.

Content Categorization: Using keywords to classify content helps understand what's available on Netflix.
