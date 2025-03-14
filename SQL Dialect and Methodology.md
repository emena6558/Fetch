# SQL Dialect and Methodology
## SQL Dialect Used: Snowflake SQL
In this project, I used Snowflake SQL to parse JSON files, create tables, and load data into a Snowflake data warehouse. The steps outlined below demonstrate how I used Snowflake's internal stages, file formats, and JSON parsing capabilities to create the necessary tables. Each query was developed using Snowflake's standard SQL syntax, including functions like parse_json(), to_timestamp(), and LATERAL FLATTEN() for handling JSON structures.

## Step 1: Create an Internal Stage and Upload JSON Files
* Create an internal stage to upload JSON files into Snowflake.
* Upload JSON files using Snowflake UI to the stage for processing (users.json, receipts.json, brands.json).
```
create stage fetch;

CREATE FILE FORMAT JSON
  TYPE = 'JSON'
  COMPRESSION = 'AUTO' 
;
```
![image](https://github.com/user-attachments/assets/36d159ec-4fa0-4940-bd4a-a49493500a01)

## Step 2: Create the Tables and Parse Data

1. Create **USERS** Table
   
```
CREATE OR REPLACE TABLE USERS AS
SELECT
    value:_id."$oid"::STRING AS id,
    value:active::BOOLEAN AS active,
    to_timestamp(value:createdDate."$date"::INT /1000) AS created_date,
    to_timestamp(value:lastLogin."$date"::INT /1000) AS last_login,
    value:role::STRING AS role,
    value:signUpSource::STRING AS sign_up_source,
    value:state::STRING AS state
FROM 
    (SELECT parse_json($1) as value FROM '@FETCH/users.json' (file_format => 'JSON'));
```

2. Create **RECEIPTS** Table

```
CREATE OR REPLACE TABLE RECEIPTS AS
SELECT
     value:_id."$oid"::STRING AS id
    ,value:bonusPointsEarned::INT AS BONUS_POINTS_EARNED
    ,value:bonusPointsEarnedReason::STRING AS BONUS_POINT_REASON
    ,to_timestamp(value:createDate."$date"::INT /1000) AS CREATED_DATE
    ,to_timestamp(value:dateScanned."$date"::INT /1000) AS DATE_SCANNED
    ,to_timestamp(value:finishedDate."$date"::INT /1000)AS FINISHED_DATE
    ,to_timestamp(value:modifyDate."$date"::INT /1000)AS MODIFY_DATE
    ,to_timestamp(value:pointsAwardedDate."$date"::INT /1000)AS POINTS_AWARDED_DATE
    ,value:pointsEarned::INT AS POINTS_EARNED
    ,to_timestamp(value:purchaseDate."$date"::INT /1000) AS PURCHASED_DATE
    ,value:purchasedItemCount::INT AS PURCHASED_ITEM_COUNT
    ,CASE WHEN value:rewardsReceiptItemList::VARIANT IS NULL THEN 0 ELSE 1 END AS REWARDS_RECEIPT_ITEM_LIST_FLAG
    ,value:rewardsReceiptStatus::STRING AS REWARDS_RECEIPTS_STATUS
    ,value:totalSpent::INT AS TOTAL_SPENT
    ,value:userId::STRING AS USER_ID
FROM
    (SELECT parse_json($1) as value FROM '@FETCH/receipts.json' (file_format => 'JSON'));
```

3. Create **REWARDS_RECEIPT_ITEM_LIST** Table

```
CREATE OR REPLACE TABLE REWARDS_RECEIPT_ITEM_LIST AS
SELECT
     item.value:barcode::STRING as BARCODE 
    ,item.value:description::STRING as DESCRIPTION
    ,item.value:finalPrice::FLOAT AS FINAL_PRICE
    ,item.value:itemPrice::FLOAT AS ITEM_PRICE
    ,item.value:needsFetchReview::BOOLEAN AS NEEDS_FETCH_REVIEW
    ,item.value:partnerItemId::STRING AS PARTNER_ITEM_ID
    ,item.value:preventTargetGapPoints::BOOLEAN AS PREVENT_TARGET_GAP_POINTS
    ,item.value:quantityPurchased::INT AS QUANTITY_PURCHASED
    ,item.value:userFlaggedBarcode::STRING AS USER_FLAGGED_BARCODE
    ,item.value:userFlaggedNewItem::BOOLEAN AS USER_FLAGGED_NEW_ITEM
    ,item.value:userFlaggedPrice::FLOAT AS USER_FLAGGED_PRICE
    ,item.value:userFlaggedQuantity::INT AS USER_FLAGGED_QUANTITY
    ,item.value:needsFetchReviewReason::STRING AS NEEDS_FETCH_REVIEW_REASON
    ,item.value:pointsNotAwardedReason::STRING AS POINTS_NOT_AWARDED_REASON
    ,item.value:pointsPayerId::STRING AS POINTS_PAYER_ID
    ,item.value:rewardsGroup::STRING AS REWARDS_GROUP
    ,item.value:rewardsProductPartnerId::STRING AS REWARDS_PRODUCT_PARTNER_ID
    ,item.value:userFlaggedDescription::STRING AS USER_FLAGGED_DESCRIPTION
    ,t.value:_id."$oid"::STRING AS RECEIPT_ID

FROM
    (SELECT parse_json($1) as value FROM '@FETCH/receipts.json' (file_format => 'JSON')) t,
    LATERAL FLATTEN(input => t.value:rewardsReceiptItemList) AS item;
```

3. Create **REWARDS_RECEIPT_ITEM_LIST** Table

```
CREATE OR REPLACE TABLE BRANDS AS
SELECT
    value:_id."$oid"::STRING AS ID,
    value:barcode::STRING AS BARCODE,
    value:brandCode::STRING AS BRAND_CODE,
    value:name::STRING AS NAME,
    value:category::STRING AS CATEGORY,
    value:categoryCode::STRING AS CATEGORY_CODE,
    value:topBrand::BOOLEAN AS TOP_BRAND,
    value:cpg."$id"."$oid"::STRING AS CPG_ID,
    value:cpg."$ref"::STRING AS CPG_REF,
FROM 
    (SELECT parse_json($1) AS value
     FROM @FETCH/brands.json 
     (file_format => 'JSON'));
```
