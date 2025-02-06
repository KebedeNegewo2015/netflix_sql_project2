# Netflix Movies and TV shows data analysis usind microsoft SQL server.

![Netflix Logo](https://github.com/KebedeNegewo2015/netflix_sql_project2/blob/main/Logonetflix.png)

## Overview

This project involves a comprehensive analysis of  Netflix Movies and TV shows data using microsoft SQL server.
the goal is to extract valuable insights and answer from the dataset by using sql skills.
the following README file is provides you detail objective of the project that including all questions, answers and 
final conclusions.


## Ojectives
- identifying most movies and TV shows that release in specific country.
- listing how many movies or tv shows are released every year.
- finding actors playing most movies or TV shows.
- extracting longest movies.
- categorizing movies or TV shows on specific categories

## Dataset

source data provided from Kaggle dataset.
data source link : https://www.kaggle.com/datasets/paramvir705/netflix-dataset/data 

## Business problem and Solutions

## 1. Count the number of Movies vs TV Shows
```SQL
SELECT 
	type,
	count(*) as Total_content FROM netflix
GROUP BY type;
```


## 2. Find the most common rating for movies and TV shows
```sql	
 SELECT 
	type,
	rating, 
	total_count FROM
	(	
		SELECT 
		type,
		rating,
		COUNT(*) as total_count,
		RANK() OVER (PARTITION BY type ORDER BY count(*) DESC) as ranking
		FROM netflix
		GROUP BY type, rating
	) as table1
	WHERE ranking = 1
```


## 3. List all movies released in a specific year (e.g., 2021)
```sql	
SELECT 
    *
 FROM netflix
WHERE  
	type = 'Movie'
	AND 
	release_year = 2021 
```
	
## 4. Find the top 5 countries with the most content on Netflix

```sql	
SELECT top 5
	 country 
	, COUNT(value) as countries
FROM netflix
CROSS APPLY
	string_split(country, ',')
	where country not like ''
	group by country
	order by countries DESC
```
## 5. Identify the longest movie

```sql	
SELECT top 1 *,
cast(SUBSTRING(duration, 1, charindex(' ', duration)) as int ) as longestMovies
 FROM netflix
WHERE type = 'movie'
ORDER BY longestMovies  DESC
```
## 6. Find content added in the last 5 years
```sql	
SELECT 
	* 
FROM netflix
WHERE  
	date_added >= dateadd(year, -5, getdate())
```
## 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

```sql	
SELECT 
	* 
FROM netflix
WHERE 
	director LIKE '%Rajiv Chilaka%' 
```
## 8. List all TV shows with more than 5 seasons

```sql	
SELECT 
	* 
FROM netflix
WHERE
	type ='TV show'
	AND
	substring(duration, 1, charindex(' ', duration))  >= 5
```
## 9. Count the number of content items in each genre

```sql	
SELECT 
	  DISTINCT value as genre
	 ,COUNT(*) OVER (PARTITION BY value) as total_content
FROM netflix
CROSS APPLY
	STRING_SPLIT(listed_in, ',') as new
```
	
## 10.Find each year and the average numbers of content 
## release in India on netflix. 
## return top 5 year with highest avg content release!

```sql	
SELECT top 5
	YEAR(date_added) as year
	,cast(COUNT(*) as decimal(10,2)) AS total_content
	,COUNT(*)/(SELECT cast(COUNT(*) as decimal(10,2)) FROM netflix WHERE country = 'India') * 100 avg_Content_year
FROM netflix 
	WHERE country = 'india'
	GROUP BY year(date_added)
	ORDER BY avg_Content_year DESC
```
## 11. List all movies that are documentaries

```sql	
SELECT 
	*
FROM netflix
WHERE
	listed_in like '%Documentaries%'
```
## 12. Find all content without a director

```sql	
SELECT 
	* 
FROM netflix
WHERE 
	director is null
```


## 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

```sql	
SELECT 
	* 
FROM netflix
WHERE
	casts like '%Salman Khan%' 
	AND
	date_added > DATEADD(YEAR, -10, GETDATE())
```

## 14. Find the top 10 actors who have appeared in 
## the highest number of movies produced in United States.


```sql	
SELECT  top 11 COUNT(casts) as numberofmoviesplayed
		,value
FROM netflix
cross apply
string_split(casts, ',')
WHERE 
	country LIKE '%United States%'
	GROUP by  value
	order by numberofmoviesplayed desc
```

## 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
## the description field. Label content containing these keywords as 'Bad' and all other 
## content as 'Good'. Count how many items fall into each category.

```sql	
WITH New_table
AS
(
	SELECT 
	*, 
	CASE 
		WHEN description like '%Kill%' 
		OR
		description like '%Violence%' THEN 'Bad_Content' 
		ELSE 'good_Content' 
	END  categories
FROM netflix
) 
select categories, COUNT(*) as total_Content from New_table 
GROUP BY categories
```
