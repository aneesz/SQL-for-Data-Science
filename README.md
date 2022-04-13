# My Data Science Portfolio

SQL-for-Data-Science


1.	Compare the final_assignments_qa table to the assignment events we captured for user_level_testing. Write an answer to the following question: Does this table have everything you need to compute metrics like 30-day view-binary?


SELECT 
  * 
FROM 
  dsv1069.final_assignments_qa

 Answer: NO, the created_at date is needed.

REFORMAT THE DATA

SELECT 	item_id,
  		test_a       AS test_assignment, 
  		'test_a'     AS test_number, 
  		'2022-01-01' AS test_start_date
FROM 
  dsv1069.final_assignments_qa
  LIMIT 10

 
2.	Write a query and table creation statement to make final_assignments_qa look like the final_assignments table. If you discovered something missing in part 1, you may fill in the value with a place holder of the appropriate data type. 
SELECT	 item_id,
       		test_a AS test_assignment,
       	(CASE
           	 WHEN test_a IS NOT NULL then 'test_a'
          	ELSE NULL
        	END) AS test_number,
     	(CASE
            	WHEN test_a IS NOT NULL then '2015-01-05 00:00:00'
            	ELSE NULL
        	END) AS test_start_date
FROM dsv1069.final_assignments_qa
UNION
SELECT 	item_id,
       		test_b AS test_assignment,
       (CASE
            WHEN test_b IS NOT NULL then 'test_b'
            ELSE NULL
            END) AS test_number,
       (CASE
            WHEN test_b IS NOT NULL then '2015-01-05 00:00:00'
            ELSE NULL
            END) AS test_start_date
FROM dsv1069.final_assignments_qa
UNION
SELECT		 item_id,
       		test_c AS test_assignment,
       (CASE
            WHEN test_c IS NOT NULL then 'test_c'
            ELSE NULL
            END) AS test_number,
       (CASE
            WHEN test_c IS NOT NULL then '2015-01-05 00:00:00'
            ELSE NULL
            END) AS test_start_date
FROM dsv1069.final_assignments_qa
UNION
SELECT 	item_id,
       		test_d AS test_assignment,
       (CASE
            WHEN test_d IS NOT NULL then 'test_d'
            ELSE NULL
            END) AS test_number,
       (CASE
            WHEN test_d IS NOT NULL then '2015-01-05 00:00:00'
            ELSE NULL
            END) AS test_start_date
FROM dsv1069.final_assignments_qa
UNION
SELECT 	item_id,
      		 test_e AS test_assignment,
       (CASE
            WHEN test_e IS NOT NULL then 'test_e'
            ELSE NULL
            END) AS test_number,
       (CASE
            WHEN test_e IS NOT NULL then '2015-01-05 00:00:00'
            ELSE NULL
            END) AS test_start_date
FROM dsv1069.final_assignments_qa
UNION
SELECT 	item_id,
      		 test_f AS test_assignment,
       (CASE
            WHEN test_f IS NOT NULL then 'test_f'
            ELSE NULL
            END) AS test_number,
       (CASE
            WHEN test_f IS NOT NULL then '2015-01-05 00:00:00'
            ELSE NULL
            END) AS test_start_date
FROM dsv1069.final_assignments_qa;
 

3.	Use the final_assignments table to calculate the order binary for the 30-day window after the test assignment for item_test_2 (You may include the day the test started)

SELECT 	test_assignment,
       COUNT(DISTINCT item_id) AS number_of_items,
       SUM(order_binary) AS items_ordered_30d
FROM
  (SELECT	item_test_2.item_id,
         		item_test_2.test_assignment,
          		item_test_2.test_number,
          		item_test_2.test_start_date,
          		item_test_2.created_at,
          MAX(CASE
                  WHEN (created_at > test_start_date
              AND DATE_PART('day', created_at - test_start_date) <= 30) THEN 1
                  ELSE 0
             	   END) AS order_binary
   FROM
     (SELECT 	final_assignments.*,
            DATE(orders.created_at) AS created_at
      FROM dsv1069.final_assignments AS final_assignments
      LEFT JOIN dsv1069.orders AS orders
        ON final_assignments.item_id = orders.item_id
        WHERE test_number = 'item_test_2') AS item_test_2
   GROUP BY item_test_2.item_id,
            item_test_2.test_assignment,
            item_test_2.test_number,
            item_test_2.test_start_date,
            item_test_2.created_at) AS order_binary
GROUP BY test_assignment;

		 
4.	Use the final_assignments table to calculate the view binary, and average views for the 30-day window after the test assignment for item_test_2. (You may include the day the test started)

SELECT 	item_test_2.item_id,
       		item_test_2.test_assignment,
       		item_test_2.test_number,
       MAX(CASE
               WHEN (view_date > test_start_date
                     AND DATE_PART('day', view_date - test_start_date) <= 30) THEN 1
                    ELSE 0
                    END) AS view_binary
FROM
  (SELECT final_assignments.*,
          DATE(events.event_time) AS view_date
   FROM dsv1069.final_assignments AS final_assignments
   LEFT JOIN
       (SELECT event_time,
               CASE
                   WHEN parameter_name = 'item_id' THEN CAST(parameter_value AS   NUMERIC)
                   ELSE NULL
               END AS item_id
      FROM dsv1069.events
      WHERE event_name = 'view_item') AS events
     ON final_assignments.item_id = events.item_id
   WHERE test_number = 'item_test_2') AS item_test_2
GROUP BY	item_test_2.item_id,
        		item_test_2.test_assignment,
         		item_test_2.test_number

 ![image](https://user-images.githubusercontent.com/97166392/163170756-057e7a92-64c3-461a-a8aa-6065093a337c.png)


5.	Use the https://thumbtack.github.io/abba/demo/abba.html to compute the lifts in metrics and the p-values for the binary metrics ( 30 day order binary and 30 day view binary) using a interval 95% confidence. 

 


6.	Use Mode’s Report builder feature to write up the test. Your write-up should include a title, a graph for each of the two binary metrics you’ve calculated. The lift and p-value (from the AB test calculator) for each of the two metrics, and a complete sentence to interpret the significance of each of the results.
 


The A/B testing required from this link: 
https://thumbtack.github.io/abba/demo/abba.html
 

I calculated the p-value for a 95% confidence interval. There was an improvement of 2.3% in the conversion rate of products with the new ad. With a p value of .25 the results can’t be said to be statistically significant. Therefore, it is difficult to conclude that the variation in conversion rate was caused by the new ad.
