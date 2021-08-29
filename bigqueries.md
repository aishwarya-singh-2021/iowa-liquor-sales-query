## Iowa Sales Query
>To showcase the working of joins, I have divided this dataset into three datasets namely sales,store and item. 
>Use the Data fetch code for creating these three tables.

## Data Fetch
SELECT invoice_and_item_number, date, store_number, item_number, state_bottle_cost as cost, state_bottle_retail as retail, bottles_sold, sale_dollars, volume_sold_liters, volume_sold_gallons
FROM `bigquery-public-data.iowa_liquor_sales.sales`

SELECT store_number,
        MIN(store_name) AS name, MIN(address) as address, MIN(city) as city, 
        MIN(zip_code) as zip, MIN(store_location) as location, 
        MIN(county_number) as county_code, MIN(county) as county
FROM `bigquery-public-data.iowa_liquor_sales.sales`
GROUP BY store_number

SELECT item_number, 
        MIN(category) as category_id, MIN(category_name) as category, 
        MIN(vendor_number) as vendor_id, MIN(vendor_name) as vendor, 
        MIN(item_description) as description, MIN(pack) as pack, MIN(bottle_volume_ml) as bottle_volume_ml
FROM `bigquery-public-data.iowa_liquor_sales.sales`
GROUP BY item_number

## Objective
>A new store has to be opened. For this, most prefrrable cities and most prefrrable type of items in those cities should be researched upon. Let us try to answer some questions to fulfill this objective.

## Questions
### Q.1-How many stores in a city?

SELECT * FROM `assignment-29th-august-cbd.iowa_liquor_sales.store`
SELECT city, COUNT(store_number)
FROM `assignment-29th-august-cbd.iowa_liquor_sales.store`
GROUP BY city

### Q.2-What are the sales of a city?

SELECT store.city, SUM(sales.sale_dollars) AS total_sales
FROM `assignment-29th-august-cbd.iowa_liquor_sales.store` AS store
JOIN `assignment-29th-august-cbd.iowa_liquor_sales.sales` AS sales
USING (store_number)
GROUP BY store.city
ORDER BY total_sales desc  

### Q.3-Sales/Store ratio in a city
SELECT city, total_stores as stores, total_sales/total_stores AS sales_per_store
FROM
    (
        SELECT store.city,
            SUM(sales.sale_dollars) AS total_sales, 
            COUNT(store.store_number) AS total_stores
        FROM `assignment-29th-august-cbd.iowa_liquor_sales.store` AS store
        JOIN `assignment-29th-august-cbd.iowa_liquor_sales.sales` AS sales
        USING (store_number)
        GROUP BY store.city
    )
ORDER BY sales_per_store desc

### Q.4-What are the sales of an item category
SELECT item.category_id, item.category, 
        SUM(sales.bottles_sold) AS bottles,
        SUM(sales.sale_dollars)/100000 AS total_sales
    FROM      `assignment-29th-august-cbd.iowa_liquor_sales.sales` AS sales
    LEFT JOIN `assignment-29th-august-cbd.iowa_liquor_sales.item`  AS item
    USING (item_number)
    GROUP BY  item.category_id, item.category
    ORDER BY  total_sales DESC
    
### Q.5-What is the most preferred item category in a city?

