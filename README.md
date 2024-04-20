# `Channel Portfolios` & `Business Patterns Analysis`
This is Part 2 of the previous project on Website Performance Analysis. Now in this SQL Project I explored more deeper concepts of business insights.
This is a SQL Project which uses the `Advanced SQL` concepts for Analysis. This Project focuses on gathering insights from a database and storytelling is done by me in this Project.
## Topic Covered in this Project:
| Sr no.       |  Used                                           | Sr no.       |  Used                                          |
| ------------- |:---------------------------------------------:| ------------- |:---------------------------------------:|
| `1`           |  *Analyzing Channel Portfolios*               | `5`           |  *Analyzing Product level website pathing*      |
| `2`           |  *Analyzing direct, brand driven traffic*     | `6`           |  *Cross-selling and Product Portfolio Analysis* |
| `3`           |  *Analyzing Business Patterns and Seasonality*| `7`           |  *User Level Analysis*                         |
| `4`           |  *Product level Analysis*                      | `8`           |  *Analyzing Repeat Visit & Purchase Behaviour*  |


## Let's understand the database first:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/6369f5e3-c85e-4b59-81a7-8a8e50e95ab9)
- we will be focusing on these tables only in the database.
- In this, Analysis There will 7 Question to be Answered by me as a **Business Analyst** to the **Website Manager** (Lavkush Yadav).
- This Project will be like a conversation in which I am solving issues and finding insights from the Tables of Database.

  ## Explore the Part 1 of this project too
  ## Part 1 focuses on `Website Performances` and `Traffic Analysis.`

## `Question 1:`
### First, I’d like to show our volume growth. Can you pull overall session and order volume, trended by quarter for the life of the business? Since the most recent quarter is incomplete, you can decide how to handle it. 
### Analysis:
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions, 
    COUNT(DISTINCT orders.order_id) AS orders
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/efbd224f-653a-4815-9589-952e02e54bfe)

### Conclusion:
- Now as we are at the end of 3 years in business . we can see dramatic growth. We can see 60 orders from 1st Quarter. And in 2015 it has been increased 100 times more orders. There is similar growth in other Quarters too which is really impressive. 

## `Question 2:`
### Next, let’s showcase all of our efficiency improvements. I would love to show quarterly figures since we launched, for session-to-order conversion rate, revenue per order, and revenue per session. 
### Analysis:
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS session_to_order_conv_rate, 
    SUM(price_usd)/COUNT(DISTINCT orders.order_id) AS revenue_per_order, 
    SUM(price_usd)/COUNT(DISTINCT website_sessions.website_session_id) AS revenue_per_session
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/859807f5-0c46-4892-9134-699998197b89)

### Conclusion:
- Session growth Is a great story for this business as conversion rate of session to order grown from 3% to 8%.
- And Revenue per order increased from 49 dollars to above 60 dollars and in this the company started with only one product initially and they done some cross-selling and after optimization Revenue increased.
- Also Revenue per session grown from 1 dollars and it goes all the way up to 5 dollars.  

## `Question 3:`
### I’d like to show how we’ve grown specific channels. Could you pull a quarterly view of orders from Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in?  
### Analysis:
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS gsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS bsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) AS brand_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) AS organic_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) AS direct_type_in_orders
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/ca5a06d4-bdfd-4a9c-a880-eab8b8b73f3e)

### Conclusion:
- No Surprise Here as we have seen tremendous growth over sessions previously.
- So it was coming from all of these channels.
- I think most of Investors are particularly excited about brand search, organic search and direct type in  orders are picking up.
- Let focus on 2012 2nd quarter gsearch which high at that time and others channels (brand, organic, direct type in) orders were too less about 6:1 ratio.
- And in 2015 1st Quarter  we can see gsearch and others ratios lies around 2:1 .
- Now it is less dependent on paid campaign.

## `Question 4:` 
### Next, let’s show the overall session-to-order conversion rate trends for those same channels, by quarter. Please also make a note of any periods where we made major improvements or optimizations.
 
### Analysis: 
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END)
		/COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS gsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS bsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN website_sessions.website_session_id ELSE NULL END) AS brand_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN website_sessions.website_session_id ELSE NULL END) AS organic_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN website_sessions.website_session_id ELSE NULL END) AS direct_type_in_conv_rt
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/de9981a8-fda2-449a-afdd-1906dd8f12f0)

### Conclusion:
- We can see gsearch conversion rate which increased from 3% to 8% in recent Quarters.
- Bsearch growth have same story too.
- Brand, organic and direct type in conversion rate all of them have seen substantial improvements from where they were initially to where they are now.
- It shows company is really working in growth rate to improve business.


## `Question 5:`
### We’ve come a long way since the days of selling a single product. Let’s pull monthly trending for revenue and margin by product, along with total sales and revenue. Note anything you notice about seasonality.
### Analysis:

```
SELECT
	YEAR(created_at) AS yr, 
    MONTH(created_at) AS mo, 
    SUM(CASE WHEN product_id = 1 THEN price_usd ELSE NULL END) AS mrfuzzy_rev,
    SUM(CASE WHEN product_id = 1 THEN price_usd - cogs_usd ELSE NULL END) AS mrfuzzy_marg,
    SUM(CASE WHEN product_id = 2 THEN price_usd ELSE NULL END) AS lovebear_rev,
    SUM(CASE WHEN product_id = 2 THEN price_usd - cogs_usd ELSE NULL END) AS lovebear_marg,
    SUM(CASE WHEN product_id = 3 THEN price_usd ELSE NULL END) AS birthdaybear_rev,
    SUM(CASE WHEN product_id = 3 THEN price_usd - cogs_usd ELSE NULL END) AS birthdaybear_marg,
    SUM(CASE WHEN product_id = 4 THEN price_usd ELSE NULL END) AS minibear_rev,
    SUM(CASE WHEN product_id = 4 THEN price_usd - cogs_usd ELSE NULL END) AS minibear_marg,
    SUM(price_usd) AS total_revenue,  
    SUM(price_usd - cogs_usd) AS total_margin
FROM order_items 
GROUP BY 1,2
ORDER BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/1be62ce5-88f7-4a06-9cb0-400278584006)
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/4ad3287c-e46b-4a0c-bffa-f9ebad8f4ca1)

### Conclusion:
- We can see mrfuzzy revenue was approx. 3000 dollar initially  and it was increased to 43000 dollar approx. in 2015 Quarter.
- Focusing on 2014 Nov and Dec month it has highest sales.
- Love Bear Toy sales was highest in the month of February as this bear is targeted to couples for giving this toys as a gift.
- Rest products shows major sales in the end of years.

## `Question 6:`
### Let’s dive deeper into the impact of introducing new products. Please pull monthly sessions to the /products page, and show how the % of those sessions clicking through another page has changed over time, along with a view of how conversion from /products to placing an order has improved.
 
### Analysis:
### `Part 1:`
```
CREATE TEMPORARY TABLE products_pageviews
SELECT
	website_session_id, 
    website_pageview_id, 
    created_at AS saw_product_page_at

FROM website_pageviews 
WHERE pageview_url = '/products';
SELECT * FROM products_pageviews;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/19fff257-5a17-443f-ad5e-e944026ad2db)

### Conclusion:
- We have whole lots of session and website pageviews and when they saw that product page. 

### `Part 2:`
```
SELECT 
	YEAR(saw_product_page_at) AS yr, 
    MONTH(saw_product_page_at) AS mo,
    COUNT(DISTINCT products_pageviews.website_session_id) AS sessions_to_product_page, 
    COUNT(DISTINCT website_pageviews.website_session_id) AS clicked_to_next_page, 
    COUNT(DISTINCT website_pageviews.website_session_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS clickthrough_rt,
    COUNT(DISTINCT orders.order_id) AS orders,
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS products_to_order_rt
FROM products_pageviews
	LEFT JOIN website_pageviews 
		ON website_pageviews.website_session_id = products_pageviews.website_session_id -- same session
        AND website_pageviews.website_pageview_id > products_pageviews.website_pageview_id -- they had another page AFTER
	LEFT JOIN orders 
		ON orders.website_session_id = products_pageviews.website_session_id
GROUP BY 1,2;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/bcdd81dd-c46f-4729-abf8-6967a4001d2b)

### Conclusion:
- So what we see here is  year, month, session to product page,  clicked to next page, clickthrough rate, orders, product to order rate.
- Main thing to observe here is clickthrough rate which was initially 71% which was increased to 85% in most recent months.
- Similarly in people seeing products and ordering it rate is increased from 8% to 14% over a year. 


## `Question 7:`
### We made our 4th product available as a primary product on December 05, 2014 (it was previously only a cross-sell item). Could you please pull sales data since then, and show how well each product cross-sells from one another?
- This Question is breakdown in 3 parts for better understanding.
 
### Analysis:
### `Part 1:`
```
CREATE TEMPORARY TABLE primary_products
SELECT 
	order_id, 
    primary_product_id, 
    created_at AS ordered_at
FROM orders 
WHERE created_at > '2014-12-05'; -- when the 4th product was added (says so in question)
SELECT * FROM primary_products;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/4c37f3b1-7770-480c-8017-3ec7daee9133)

### Conclusion:
- Here I have extracted primary product id and order timing.

### `Part 2:`
```
SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0; -- only bringing in cross-sells;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/8e76ca2e-6aa6-46e0-b883-e4989fc12949)

### Conclusion:
- And now what we see in this query that we have a order id, primary product id and which products was cross sold.
- We can observe product 4 and 2 are cross sold. Null value shows the product wasn’t cross sold.  

### `Part 3:`
```
SELECT 
	primary_product_id, 
    COUNT(DISTINCT order_id) AS total_orders, 
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END) AS _xsold_p1,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END) AS _xsold_p2,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END) AS _xsold_p3,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END) AS _xsold_p4,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p1_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p2_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p3_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p4_xsell_rt
FROM
(
SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0 -- only bringing in cross-sells
) AS primary_w_cross_sell
GROUP BY 1;
```
### Output:
![image](https://github.com/saurav2021c/Business_Patterns_Analysis/assets/97289683/08628d03-5669-403c-b88f-8e629be67c90)

### Conclusion:
- We got here the primary product id which I have grouped by product 1,2,3,4.
- Then we have total orders of each products.
- We can observe here that product 1 was never cross sold when product 1 was the primary product.
- After that Product 1 was cross sold with product 2 ,3,4 in some amount.
- Seeing Cross selling of Product 4 which was sold in high volumes.
- One of the reason was Product 4 have low price so its cross selling increased.
- Then the same numbers were translated into rates as to the percentage of the total orders.
- Here Product 3 and 4 was cross sold really well.



# THE END
![image](https://github.com/saurav2021c/Website_Performance_SQL-Project/assets/97289683/d28fb664-104e-4373-90ed-74d165371a93)
<p>
 <a href="https://www.linkedin.com/in/sauravsinghhhh/" target="_blank" rel="noreferrer"><img src="https://github.com/saurav2021c/Portfolio-project/blob/master/src/images/LinkedIN_black.png" width="35" /></a>
</p>

