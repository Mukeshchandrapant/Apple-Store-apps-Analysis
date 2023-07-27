# Apple-Store-apps-Analysis
SQL project to Analyze the apple store application to higher management to get insight about market Insights

In this project we are analyzing the Apple store dataset WHERE we have 2 datasets
1. Apple Store - This CSV file contains all the relavent GENRE wise information about the application including key parameters
2. appleStore_description - This CSV file contains data about ID & Application description.

HERE are the key Questions that we are solving in the Session. 

Question 1. -- Check the number of unique applications in both tableAPP store.
SELECT count(DISTINCT id) as uniqueIDs
FROM AppleStore

SELECT count(DISTINCT id) as uniqueIDs
FROM AppleStore_description_combined

Question 2.-- Check For any missing values in the key fieldsAppleStor.

SELECT COUNT(*)  as missing_values
FROm AppleStore
WHERE track_name is NULL OR user_rating  is NULL or prime_genre is NULL;

SELECT COUNT(*)  as missing_values
FROm AppleStore_description_combined
WHERE app_desc is NULL;

Question 3. -- Find out the number of apps per Genre-- 
SELECT prime_genre, COUNT(*) AS number_of_apps
FROM AppleStore
GROUP BY prime_genre
ORDER BY number_of_apps DESC;

--  Question 4.- GET an overview of the apps RATING-- 

SELECT 	min(user_rating) as min_rating,
		max(user_rating) as max_rating,
        avg(user_rating) as AVG_rating
FROM AppleStore;

-- Question 5. - FINDING the insights from the DATASETS- 

-- DETERMINE whether PAID apps have higher rating than FREE apps
SELECT CASE
			WHEN price > 0 THEN 'PAID'
            ELSE 'FREE'
       END as app_type,
       avg(user_rating) as avg_rating
 FROM AppleStore
 GROUP BY app_type;
 
 --Question 6. CHECK if apps with more supported languages has higher rating.
 SELECT CASE
 			WHEN lang_num < 10 THEN 'Less than 10 Languages'
            WHEN lang_num BETWEEN 10 AND 30 THEN '10-30 Languages'
            ELSE 'Greater THAN 30 Languages'
        END AS language_bucket,
   		avg(user_rating) as avg_rating
FROM AppleStore
GROUP BY language_bucket
ORDER BY avg_rating DESC;

--Question 7.CHECK genre with low ratings--AppleStore
SELECT prime_genre, avg(user_rating) as avg_rating
FROM AppleStore
GROUP BY prime_genre
ORDER BY avg_rating LIMIT 5;

--Question 8. CHECK if there is a correlation between the length of the app description and user rating..AppleStore

SELECT CASE	
			WHEN length(ac.app_desc) < 500 THEN 'Short Description'
            WHEN length(ac.app_desc) BETWEEN 500 AND 1000 THEN 'Medium LENGTH'	
            ELSE 'Long'
       	END AS descritoin_length, 
        AVG(user_rating) as average_rating
FROM  AppleStore as a 
JOIN 
	AppleStore_description_combined as ac
ON a.id = ac.id
GROUP BY descritoin_length
ORDER BY average_rating DESC;

--Question 9. check the top rated apps for EACH genre-- 
SELECT 
	prime_genre,
    track_name,
    user_rating
FROM (
	SELECT 
  	prime_genre,
    track_name,
    user_rating, RANK() OVER(PARTITION BY prime_genre ORDER BY user_rating DESC, rating_count_tot DESC) as ranking
  FROM AppleStore
) AS a 
WHERE a.ranking = 1;
