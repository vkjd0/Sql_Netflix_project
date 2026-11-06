# Netflix Movies and TV Shows Data analysis

-- Netflix project

Create table netflix 
(
show_id	Varchar(10), 
type	Varchar(50),
title	Varchar(150), 
director Varchar(250), 
casts Varchar(800),
country	Varchar(150),
date_added Varchar(50),	
release_year int	,
rating	Varchar(50),
duration Varchar(20),
listed_in Varchar(100),
description Varchar(300)
);

Select * from netflix;

Select count(*) as total_count from netflix;



Select * from netflix;

select 
  count (*)distinct type as total_distinct_count from netflix

--- 1. Count number of Movie vs TV Shows 
select type,
	count(*)
from netflix
group by 1


--- 2. find the most common rating movies and TV shows

select 
 type,
 rating,
 count(*) as max
from netflix
group by 1,2
order by 3 desc


--- 3. List all movies released in a specific year (2020)

select * from netflix

select 
 type,
 title,
 release_year
from netflix
where release_year= 2020 
and type = 'Movie'

--- 4. Find the top  5 countries with most content on netflix


select * from netflix

select
	unnest(string_to_array(country, ',')) as new_country,
	count(show_id) as total_count
from netflix
group by 1
order by total_count desc



--- 5. Identify the longest movie? 

select * from netflix

select 
	type,
	title,
	duration
from netflix
where type = 'Movie'
order by duration desc

---5. Identify the longest movie?

select 
	duration,
	type
from netflix
where 
	type = 'Movie'
order by duration desc

--- 6. Find the content added in the five year?

Select * from netflix

select 
	*,
	to_date(date_added, 'Month, DD, YYYY')
from netflix
where 
	to_date(date_added, 'Month, DD, YYYY') >= current_date - interval '5 years'

--- 7. Find all the movies/tv shows by the director 'Rajiv chilaka'?

select * from netflix


select 
	*,
	unnest(string_to_array(director, ','))
from netflix
where 
	director like '%Rajiv Chilaka%'

--- 8. List all tv shows with more than 5 seasons

select 
	*
from netflix
where
	type = 'TV Show'
	and
	split_part(duration, ' ', 1)::numeric > 5
	

--- 9. Count the number of content item in each genre?

select * from netflix

select 
	count(*),
	unnest(string_to_array(listed_in, ','))
from netflix
group by unnest(string_to_array(listed_in, ','))


--- 10. Find each  year and average number of content releases by India on netflix.
--- return top 5 year with the heighest avg content release

select
	Extract(year from to_date(date_added, 'Month DD YYYY')) as Year,
	count(*),
	Round(count(*)::numeric/(select count(*) from netflix where country = 'India')::numeric * 100 ,2) as average_content
from netflix
where country = 'India'
group by 1

--- 11. List all the movies that are documentaries

select * 
from netflix
where type = 'Movie'
and
listed_in ilike '%Documentaries%'

--- 12. Find all content without a director

Select * from netflix
where
	director is null


--- 13. Find how many movies actor 'Salaman Khan' appeared in the last 10 year

select *
from netflix
where 
	casts ilike '%Salman Khan%'
	and
	release_year > Extract(Year from current_date) - 10


--- 14. Find the top 10 actor how are apeared in the highest number of movies produced in India

select 
	unnest(string_to_array(casts, ',')) as actor,
	count(*) as total_count
from netflix
where 
	country ilike '%India%'
group by 1
order by total_count desc 

--- 15. Categorize the content based on the presence of keyword 'kill' and 'voilence' in the description field. Label content containing these keyword as 'bad'
--- and all other content as 'good'. Count how many item fall into each category.

with new_table
as
(
select *,
	case
	when description ilike '%kill%' or
		description ilike '%voilence%' then 'Bad_Content'
		Else 'Good_content'
	End Category
from netflix
)
select Category,
	count(*)
from new_table
group by 1

where 
	description ilike '%kill%'
	or
	description ilike '%voilence%'
