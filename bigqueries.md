# Iowa Liquor Sales Query
* Created custom dataset : iowa_liquor_sales
* Shared data set access with : amrrs.data@gmail.com
* Note : To access the code with another id, please connect.

# Objective
> A new liquor store chain has to be opened. For this, most preferrable cities and most preferrable type of items in those cities should be researched upon. Let us try to answer some questions to fulfill this objective.

# Questions
> ## Q.1-How many stores in a city?
    SELECT city, COUNT(store_number) AS stores
        FROM `aishwarya-singh-assignment-cbd.iowa_liquor_sales.stores` 
        GROUP BY city
        ORDER BY stores DESC

> ## Q.2-What are the sales of a city?
    SELECT  stores.city, 
            SUM(sales.sale_dollars) AS total_sales
        FROM `aishwarya-singh-assignment-cbd.iowa_liquor_sales.stores` AS stores
        JOIN `aishwarya-singh-assignment-cbd.iowa_liquor_sales.sales`  AS sales
            USING (store_number)
        GROUP BY stores.city
        ORDER BY total_sales DESC

> ## Q.3-Sales/Store ratio in a city
    SELECT  city, 
            total_stores AS stores,
            total_sales/total_stores AS sales_per_store 
        FROM
        (
            SELECT  store.city,
                    SUM(sales.sale_dollars)   AS total_sales, 
                    COUNT(store.store_number) AS total_stores
                FROM `aishwarya-singh-assignment-cbd.iowa_liquor_sales.stores` AS store 
                JOIN `aishwarya-singh-assignment-cbd.iowa_liquor_sales.sales`  AS sales
                    USING (store_number)
                GROUP BY store.city
        )
        ORDER BY sales_per_store DESC

> ## Q.4-What are the sales of an item category
    SELECT  item.category_id, item.category, 
            SUM(sales.bottles_sold) AS bottles,
            SUM(sales.sale_dollars)/100000 AS total_sales
        FROM      `aishwarya-singh-assignment-cbd.iowa_liquor_sales.sales` AS sales
        LEFT JOIN `aishwarya-singh-assignment-cbd.iowa_liquor_sales.items` AS item 
            USING (item_number) 
        GROUP BY item.category_id, item.category 
        ORDER BY total_sales DESC

> ## Q.5-What is the most preferred item category in a city?
    WITH t1 AS
    (
        SELECT  store.city, item.category_id, item.category,
                SUM(sales.bottles_sold) AS bottles
            FROM      `aishwarya-singh-assignment-cbd.iowa_liquor_sales.sales`  AS sales
            LEFT JOIN `aishwarya-singh-assignment-cbd.iowa_liquor_sales.items`  AS item
                USING (item_number)
            LEFT JOIN `aishwarya-singh-assignment-cbd.iowa_liquor_sales.stores` AS store
                USING (store_number)
            GROUP BY store.city, item.category_id, item.category
    )
    SELECT t1.city, t1.category_id, t1.category, t1.bottles
        FROM t1
        INNER JOIN 
        (
            SELECT  city,
                    MAX(bottles) as max_bottles
            FROM t1
            GROUP BY city
        ) t2
        ON t1.city = t2.city AND t1.bottles = t2.max_bottles
    ORDER BY city
