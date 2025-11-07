## Practical Exam: Grocery Store Sales  
FoodYum is a grocery store chain that is based in the United States. FoodYum sells items such as produce, meat, dairy, baked goods, snacks, and other household food staples.

As food costs rise, FoodYum wants to make sure it keeps stocking products in all categories that cover a range of prices to ensure it has stock for a broad range of customers.


##  Data  

The data is available in the table `products`.  The dataset contains records of products stocked by FoodYum and their attributes.

| **Column Name**     | **Criteria** |
|----------------------|--------------|
| **product_id**       | Nominal. The unique identifier of the product.<br>Missing values are not possible due to the database structure. |
| **product_type**     | Nominal. The product category type of the product, one of five values (Produce, Meat, Dairy, Bakery, Snacks).<br>Missing values should be replaced with `"Unknown"`. |
| **brand**            | Nominal. The brand of the product (one of 7 possible values).<br>Missing values should be replaced with `"Unknown"`. |
| **weight**           | Continuous. The weight of the product in grams (positive values rounded to 2 decimal places).<br>Missing values should be replaced with the overall median weight. |
| **price**            | Continuous. The price the product is sold at, in US dollars (positive values rounded to 2 decimal places).<br>Missing values should be replaced with the overall median price. |
| **average_units_sold** | Discrete. The average number of units sold each month (positive integer values).<br>Missing values should be replaced with `0`. |
| **year_added**       | Nominal. The year the product was first added to FoodYum stock.<br>Missing values should be replaced with `2022`. |
| **stock_location**   | Nominal. The location that stock originates (one of four warehouse locations: A, B, C, or D).<br>Missing values should be replaced with `"Unknown"`. |


## Task 1  

Last year (2022) there was a bug in the product system. For some products that were added in that year, the `year_added` value was not set in the data. As the year the product was added may have an impact on the price of the product, this is important information to have.

Write a query to determine how many products have the `year_added` value missing. Your output should be a single column, `missing_year`, with a single row giving the number of missing values.

```sql
-- Write your query for task 1 in this cell
SELECT COUNT(*) AS missing_year
FROM products
WHERE year_added IS NULL;
```
<img width="1138" height="187" alt="task1_output" src="https://github.com/user-attachments/assets/add4f10f-b6a3-4327-9f74-a830046a4288" /><p align="center"><b>Output: Task 1</b></p>

## Task 2

Given what you know about the year_added data, you need to make sure all of the data is clean before you start your analysis.
The table below shows what the data should look like.

Write a query to ensure the product data matches the description provided.
Do not update the original table.

| **Column Name**     | **Criteria** |
|----------------------|--------------|
| **product_id**       | Nominal. The unique identifier of the product.<br>Missing values are not possible due to the database structure. |
| **product_type**     | Nominal. The product category type of the product, one of five values (Produce, Meat, Dairy, Bakery, Snacks).<br>Missing values should be replaced with `"Unknown"`. |
| **brand**            | Nominal. The brand of the product (one of 7 possible values).<br>Missing values should be replaced with `"Unknown"`. |
| **weight**           | Continuous. The weight of the product in grams (positive values rounded to 2 decimal places).<br>Missing values should be replaced with the overall median weight. |
| **price**            | Continuous. The price the product is sold at, in US dollars (positive values rounded to 2 decimal places).<br>Missing values should be replaced with the overall median price. |
| **average_units_sold** | Discrete. The average number of units sold each month (positive integer values).<br>Missing values should be replaced with `0`. |
| **year_added**       | Nominal. The year the product was first added to FoodYum stock.<br>Missing values should be replaced with `2022`. |
| **stock_location**   | Nominal. The location that stock originates (one of four warehouse locations: A, B, C, or D).<br>Missing values should be replaced with `"Unknown"`. |


```sql
SELECT
    product_id,
    COALESCE(product_type, 'Unknown') AS product_type,
    COALESCE(NULLIF(REPLACE(brand, '-', ''), ''), 'Unknown') AS brand,
    COALESCE(ROUND(CAST(REGEXP_REPLACE(weight, '[^\d.]', '', 'g') AS DECIMAL(10, 2)), 2), ROUND((SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY CAST(REGEXP_REPLACE(weight, '[^\d.]', '', 'g') AS DECIMAL(10, 2))) FROM products), 2)) AS weight,

COALESCE(
    TO_CHAR(CAST(price AS DECIMAL(10, 2)), '9999999999.99'),
    TO_CHAR(CAST((SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY price) FROM products) AS DECIMAL(10, 2)), '9999999999.99')
) AS price,

    COALESCE(average_units_sold, 0) AS average_units_sold,
    COALESCE(year_added, 2022) AS year_added,
    COALESCE(UPPER(stock_location), 'Unknown') AS stock_location
FROM products;
```
<img width="1127" height="722" alt="task2_output" src="https://github.com/user-attachments/assets/8b91c37c-6c27-48db-b942-5133e2730c5e" /><p align="center"><b>Output: Task 2</b></p>

## Task 3

To find out how the range varies for each product type, your manager has asked you to determine the minimum and maximum values for each product type.

Write a query to return the product_type, min_price and max_price columns.
```sql
SELECT product_type,
	   MIN(price) AS min_price,
	   MAX(price) AS max_price
FROM products 
GROUP BY product_type;
```
<img width="1368" height="290" alt="task3_output" src="https://github.com/user-attachments/assets/9eb8d49a-6216-44cd-8b92-667209917b1f" /><p align="center"><b>Output: Task 3</b></p>

## Task 4

The team want to look in more detail at meat and dairy products where the average units sold was greater than ten.

Write a query to return the product_id, price and average_units_sold of the rows of interest to the team.
```sql
SELECT product_id, price, average_units_sold
FROM products 
WHERE product_type IN ('Meat', 'Dairy')
	    AND average_units_sold > 10;
```
<img width="1366" height="736" alt="task4_output" src="https://github.com/user-attachments/assets/6841533f-000e-45bc-9387-2604b8dd345c" /><p align="center"><b>Output: Task 4</b></p>
