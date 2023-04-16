
```sql
USE netflix;
```

```sql
/*Checking for null values across columns*/ 

SELECT COUNT(show_id) AS show_id_nulls
FROM netflix_titles
WHERE show_id= '' ;
-- 0 nulls

SELECT COUNT(`type`) AS type_nulls
FROM netflix_titles
WHERE `type`= '' ;
-- 0 nulls

SELECT COUNT(title) AS title_nulls 
FROM netflix_titles
WHERE title = '';
-- 0 nulls

SELECT COUNT(*) AS director_nulls
FROM netflix_titles
WHERE director = ''; 
-- 2634 nulls

SELECT COUNT(*) AS cast_nulls
FROM netflix_titles
WHERE cast = ''; 
-- 825 nulls

SELECT COUNT(country) AS country_nulls
FROM netflix_titles
WHERE country = '' ;
-- 831 nulls

SELECT COUNT(date_added) AS date_added_nulls
FROM netflix_titles
WHERE date_added = '' ;
-- 10 nulls

SELECT COUNT(release_year) AS release_year_nulls
FROM netflix_titles
WHERE release_year = '' ; 
-- 0 nulls

SELECT COUNT(rating) AS rating_nulls
FROM netflix_titles
WHERE rating = '' ;
-- 4 nulls

SELECT COUNT(duration) AS duration_nulls
FROM netflix_titles
WHERE duration = '' ;
-- 3 nulls

SELECT COUNT(listed_in) AS listed_in_nulls
FROM netflix_titles
WHERE listed_in= '' ;
-- 0 nulls

SELECT COUNT(`description`) AS description_nulls
FROM netflix_titles
WHERE `description`= '' ;
-- 0 Nulls

```
```sql
/* Out of a total 8807 records, there are approx. 30% nulls in director column, 9% in cast and country columns and negligible amount of nulls in
date_added, rating and duration.
In order to fix the nulls in director and country column, we will input the missing values as unknown. 
We are not updating the cast column as it is not necessary for our analysis and will be dropped later. */

UPDATE netflix_titles
SET director = 'Unknown'
WHERE director = '';

UPDATE netflix_titles
SET country = 'Unknown'
WHERE country = '';
```
```sql
/* The nulls in date_added, rating and duration columns are minimal and hence deleting them will not have an impact on our visualization.
Deleting null rows from above mentioned columns. */

-- Show nulls
SELECT show_id, date_added
FROM netflix_titles
WHERE date_added = '';

-- Delete nulls
DELETE FROM netflix_titles
WHERE show_id IN ('s6067', 's6175', 's6796', 's6807', 's6902', 's7197', 's7255', 's7407', 's7848', 's8183');

-- Show nulls
SELECT show_id, rating
FROM netflix_titles
WHERE rating = '';

-- Delete nulls
DELETE FROM netflix_titles
WHERE show_id IN ('s5990', 's6828', 's7313', 's6807', 's7538');

-- Show nulls
SELECT show_id, duration
FROM netflix_titles
WHERE duration = '';

-- Delete nulls
DELETE FROM netflix_titles
WHERE show_id IN ('s5542', 's5795', 's5814');
```
```sql

/*Re-checking for null values across columns and confirming number of rows are same across columns*/ 

SELECT COUNT(show_id) AS show_id_nulls
FROM netflix_titles
WHERE show_id= '' ;
-- 0 nulls

SELECT COUNT(`type`) AS type_nulls
FROM netflix_titles
WHERE `type`= '' ;
-- 0 nulls

SELECT COUNT(title) AS title_nulls 
FROM netflix_titles
WHERE title = '';
-- 0 nulls

SELECT COUNT(*) AS director_nulls
FROM netflix_titles
WHERE director = ''; 
-- 0 nulls

SELECT COUNT(*) AS cast_nulls
FROM netflix_titles
WHERE cast = ''; 
-- 825 nulls but this column will be dropped as it is not required for our analysis and visualization

SELECT COUNT(country) AS country_nulls
FROM netflix_titles
WHERE country = '' ;
-- 0 nulls

SELECT COUNT(date_added) AS date_added_nulls
FROM netflix_titles
WHERE date_added = '' ;
-- 0 nulls

SELECT COUNT(release_year) AS release_year_nulls
FROM netflix_titles
WHERE release_year = '' ; 
-- 0 nulls

SELECT COUNT(rating) AS rating_nulls
FROM netflix_titles
WHERE rating = '' ;
-- 0 nulls

SELECT COUNT(duration) AS duration_nulls
FROM netflix_titles
WHERE duration = '' ;
-- 0 nulls

SELECT COUNT(listed_in) AS listed_in_nulls
FROM netflix_titles
WHERE listed_in= '' ;
-- 0 nulls

SELECT COUNT(`description`) AS description_nulls
FROM netflix_titles
WHERE `description`= '' ;
-- 0 Nulls

SELECT *
FROM netflix_titles;
-- 8790 rows in total
```
```sql
/* Dropping cast and description columns as they are not needed for our analysis and visualization */
ALTER TABLE netflix_titles
DROP column `cast`,
DROP column `description`;
```
```sql
/* Few titles consist of multiple country names in the country column. In order to make our visualization cleaner, we will assume that only
the first country mentioned is the original country for the movie. Cleaning the country column using SUBSTRING. */

-- Adding new column for country
ALTER TABLE netflix_titles
ADD column country_final varchar(100);

-- Populating data in country_final column
UPDATE  netflix_titles
SET country_final = SUBSTRING_INDEX(country, '_', 1);

-- Dropping old country column
ALTER TABLE netflix_titles
DROP column country;

-- Renaming the country_final column to country
ALTER TABLE netflix_titles
RENAME COLUMN country_final TO country;
```
