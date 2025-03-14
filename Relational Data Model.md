# Fetch Data Model 

![image](https://github.com/emena6558/Fetch/blob/main/Data_Model.png)

## Relationships

**USERS** to **RECEIPTS**:
A one-to-many relationship, where a user can have multiple receipts.

**RECEIPTS** to **REWARDS_RECEIPT_ITEM_LIST**:
A one-to-many relationship, where each receipt can contain multiple items.

**REWARDS_RECEIPT_ITEM_LIST** to **BRANDS**:
A many-to-one relationship, where each item can be linked to a brand via the barcode.
