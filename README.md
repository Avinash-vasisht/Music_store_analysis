# SQL Music Trends Analysis

This project showcases the data analysis of music trends using SQL, integrating data from 11 tables. It highlights key insights on popular artists, trending genres, and user behavior, demonstrating advanced SQL query techniques to uncover patterns and trends in music data.

## Project Overview

1. **Comprehensive Data Integration**: Merged data from 11 tables for a holistic view.
2. **Advanced SQL Analysis**: Employed sophisticated SQL queries to derive meaningful insights.
3. **Music Trends Identification**: Analyzed popular artists, trending genres, and user listening behavior.

## Tools and Techniques
* **SQL** for data querying and analysis.
* **ER Diagram** created to understand database relationships.

## How to Use

1. Import the provided file into your database.
2. Use the SQL scripts provided to perform analysis.
3. Review the ER Diagram to understand data relationships.

## Project Questions and Queries
**Q1: Who is the senior most employee based on job title?**
```sql
SELECT	
        CONCAT(LAST_NAME,' ', FIRST_NAME) AS "Full Name", 
        LEVELS 
FROM	
        EMPLOYEE 
ORDER BY	
        2 DESC
LIMIT	
       1;
```

**Q2: Which countries have the most Invoices?**
```sql
SELECT	
         BILLING_COUNTRY,	
         COUNT(*)
FROM	
         INVOICE 
GROUP BY	
        BILLING_COUNTRY 
ORDER BY	
       2 DESC
LIMIT	
      1;
```

**Q3: What are top 3 values of total invoice?**
```sql
SELECT	
        TO_CHAR(TOTAL, '999,999.00') AS "Total" 
FROM	
       INVOICE 
ORDER BY	
      1 DESC
LIMIT	
     3;
   ```  

**Q4: Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals.**
```sql
SELECT	
         BILLING_CITY,	
         TO_CHAR(SUM(TOTAL), '999,999.00') AS "Invoice Total" 
FROM	
         INVOICE 
GROUP BY	
        BILLING_CITY 
ORDER BY	
       2 DESC 
LIMIT	
      1;
``` 

**Q5: Who is the best customer? The customer who has spent the most money will be declared the best customer. 
Write a query that returns the person who has spent the most money.**
```sql
SELECT	
        CUSTOMER.CUSTOMER_ID,	FIRST_NAME, LAST_NAME,               
        SUM(TOTAL) AS TOTAL_SPENDING
FROM	
        CUSTOMER	
        JOIN INVOICE ON CUSTOMER.CUSTOMER_ID = INVOICE.CUSTOMER_ID GROUP BY	
        CUSTOMER.CUSTOMER_ID 
ORDER BY	
        TOTAL_SPENDING DESC 
LIMIT	
        1;
``` 

**Q6: Let's invite the artists who have written the most rock music in our dataset. Write a query that returns the Artist name and total track count of the top 10 rock bands.**
```sql
SELECT	
        ARTIST.ARTIST_ID, ARTIST.NAME,                
        COUNT(ARTIST.ARTIST_ID) AS NUMBER_OF_SONGS
FROM	
       TRACK	
       JOIN ALBUM ON ALBUM.ALBUM_ID = TRACK.ALBUM_ID	
       JOIN ARTIST ON ARTIST.ARTIST_ID = ALBUM.ARTIST_ID	
       JOIN GENRE ON GENRE.GENRE_ID = TRACK.GENRE_ID 
WHERE	
       GENRE.NAME LIKE 'Rock'
GROUP BY	
       ARTIST.ARTIST_ID 
ORDER BY	
      NUMBER_OF_SONGS DESC 
LIMIT	
     10;
``` 

**Q7: Return all the track names that have a song length longer than the average song length. Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first.**
```sql
SELECT  
        NAME,   
        MILLISECONDS
FROM    
        TRACK 
WHERE    
       MILLISECONDS > (        
                     SELECT            
                         AVG(MILLISECONDS)       
                     FROM           
                        TRACK   
 )
ORDER BY MILLISECONDS DESC;
``` 

**Q8: Find how much amount spent by each customer on artists? Write a query to return customer name, artist name and total spent.**
 ```sql
 WITH	BEST_SELLING_ARTIST AS (		
           SELECT ARTIST.ARTIST_ID AS ARTIST_ID, ARTIST.NAME AS ARTIST_NAME,	                   		
                    SUM(INVOICE_LINE.UNIT_PRICE * INVOICE_LINE.QUANTITY) AS TOTAL_SALES
           FROM INVOICE_LINE			
                    JOIN TRACK ON TRACK.TRACK_ID = INVOICE_LINE.TRACK_ID			
                    JOIN ALBUM ON ALBUM.ALBUM_ID = TRACK.ALBUM_ID			
                    JOIN ARTIST ON ARTIST.ARTIST_ID = ALBUM.ARTIST_ID		
           GROUP BY 1	
           ORDER BY 3 DESC		
           LIMIT 1 )
SELECT C.CUSTOMER_ID,	C.FIRST_NAME, C.LAST_NAME,	BSA.ARTIST_NAME,	
                    SUM(IL.UNIT_PRICE * IL.QUANTITY) AS AMOUNT_SPENT 
FROM INVOICE I	
                   JOIN CUSTOMER C ON C.CUSTOMER_ID = I.CUSTOMER_ID	
                   JOIN INVOICE_LINE IL ON IL.INVOICE_ID = I.INVOICE_ID	
                   JOIN TRACK T ON T.TRACK_ID = IL.TRACK_ID	
                   JOIN ALBUM ALB ON ALB.ALBUM_ID = T.ALBUM_ID	
                   JOIN BEST_SELLING_ARTIST BSA ON BSA.ARTIST_ID = ALB.ARTIST_ID
GROUP BY	1,2,3,4
ORDER BY	5 DESC;
``` 

**Q9: We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre with the highest amount of purchases. Write a query that returns each country along with the top Genre. For countries where the maximum number of purchases is shared return all Genres.**
```sql
WITH popular_genre AS 
(
    SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name, genre.genre_id, 
	ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS RowNo 
    FROM invoice_line 
	JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
	JOIN customer ON customer.customer_id = invoice.customer_id
	JOIN track ON track.track_id = invoice_line.track_id
	JOIN genre ON genre.genre_id = track.genre_id
	GROUP BY 2,3,4
	ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM popular_genre WHERE RowNo <= 1
``` 

**Q10: Write a query that determines the customer that has spent the most on music for each country. Write a query that returns the country along with the top customer and how much they spent. For countries where the top amount spent is shared, provide all customers who spent this amount.**
```sql
WITH Customter_with_country AS (		           
           SELECT customer.customer_id, first_name,last_name, billing_country, 
           to_char(SUM(total),'999,999.99') AS total_spending, 	               
           ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(total) DESC) AS RowNo 		 
           FROM invoice		           
           JOIN customer ON customer.customer_id = invoice.customer_id		           
           GROUP BY 1,2,3,4		          
           ORDER BY 4 ASC,5 DESC) 
SELECT * FROM Customter_with_country WHERE RowNo <= 1
``` 


## Project schema
https://ibb.co/8cRFm63

## Github
https://github.com/dashboard

## Linkedin
www.linkedin.com/in/avinashvasisht
