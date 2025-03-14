# Potential Data Quality Issues Identified:
1) Barcode Duplicates for Different Products:
    * Several barcodes are associated with multiple distinct product names or brands. This is not expected behavior because, in most cases, barcodes should be unique identifiers for products.
    * For example, 511111004790 appears twice with different products ALEXA and BITTEN.
    * This impacts reporting and analytics as barcodes being reused for different products will likely lead to misleading reports about product sales, inventory, and promotions. We might end up counting the same barcode under different brands or products, which can distort the analysis.
```
select barcode,name,brand_code
from brands 
where barcode in (
511111305125
,511111504788
,511111204923
,511111605058
,511111504139
,511111004790
,511111704140
)
order by 1;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/issue1.png)

2) Missing Barcodes in REWARDS_RECEIPT_ITEM_LIST
      * Many records in the REWARDS_RECEIPT_ITEM_LIST table do not have any barcode information.
      * Without a barcode, it becomes difficult to link products in REWARDS_RECEIPT_ITEM_LIST to their corresponding brands in the BRANDS table making it challenging to track the number of items purchased by brand and other related metrics.
```
SELECT * FROM REWARDS_RECEIPT_ITEM_LIST where barcode is null;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/issue2.png)

3) Duplicates Users:
    * The id field should ideally be unique, as each user represents a unique entity in the system. Duplicate user_id entries would make it difficult to ensure that each user has only one record, leading to potential inconsistencies in user-related analyses.
```
SELECT * FROM USERS;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/issue3.png)
