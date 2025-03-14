## 1) When considering average spend from receipts with 'rewardsReceiptStatus’ of ‘Accepted’ or ‘Rejected’, which is greater?
```
select
rewards_receipts_status
,avg(total_spent) avg_spend
from receipts 
where rewards_receipts_status in ('FINISHED','REJECTED')
group by rewards_receipts_status;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/result1.png)

## 2) When considering total number of items purchased from receipts with 'rewardsReceiptStatus’ of ‘Accepted’ or ‘Rejected’, which is greater?
```
select 
rewards_receipts_status
,sum(purchased_item_count) total_item_count
from receipts 
where rewards_receipts_status in ('FINISHED','REJECTED')
group by rewards_receipts_status;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/result2.png)

## 3) Top 5 Brands by Receipts Scanned for the Most Recent Month
```
SELECT b.name AS brand, COUNT(distinct r.id) AS receipt_count
FROM receipts r
JOIN rewards_receipt_item_list rl
    ON r.id = rl.receipt_id
JOIN brands b
    ON b.barcode = rl.barcode
WHERE TO_CHAR(r.date_scanned, 'YYYY-MM') = '2021-01'
GROUP BY b.name
ORDER BY receipt_count DESC
LIMIT 5;
```
![image](https://github.com/emena6558/Fetch/blob/main/images/result3.png)

## Notes:
* Date Range Consideration:
  * The most recent month of March 2021 did not have sufficient data (only one day available) to evaluate.
  * After filtering and verifying by barcode, January 2021 had the most complete data, so we chose it for the query.
* No Duplicates:
   * The COUNT(DISTINCT r.id) ensures that each receipt is only counted once, even if multiple items from the same brand exist in a single receipt.
