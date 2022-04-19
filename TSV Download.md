# Partner Integration - TSV Export
Below you will find information on how you can download a file containing your transaction records from a specific date/time forward.

## Considerations for Use
1. You must ensure that you do not allow duplicate transations to post. The transaction.id field is unique and should be used to enforce this check.
2. For any voids and chargebacks (ACH Rejects), the “Original” transaction is sent the line above/before the void/chargeback.
    1. Ensure you have proper handling of voids, we will ALWAYS send the original transaction (again if already sent) to simplify your logic handling of these transactions. The idea here was that your system would not have to account for a situation where a check would need to be done to check for voids.
    2. ACH Rejects will also have the original transaction sent for the sake of ensuring the original transaction was posted so there is not a case where a reversal is posted without the original sale.
        1. “cb” is appended to the transaction.id.
        2. Description is prefixed with “ACH Reject-”
 

## Request Example
`GET /custom/tsvtransactions?developer-id={developer_id}&user-id={user-id}&user-api-key={user-api-key}&location_id={location_id}&from_date={from_date}`

***Note:***
- from_date
  - should be based on user's local time
  - should be the most recent date/time of the last transaction that was imported. This is important to ensure no transactions are skipped.
  - formatted like "yyyy-mm-dd hh:mm:ss"
  - should be urlencoded
 

## TSV File Columns
| Column                          | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| contact.contact_api_id          | This should be your local systems primary key for the contact                                                                                                                                                                                                                                                                                                                                                       |
| transaction.status              | Will be one of the following values: “sale” (Sale/Debit) “refund” (ach-credit, ach-refund and cc) “void” (ach and cc) “achreject” (ach chargeback) - The transaction that was rejected will share the same transaction.id however this row will be postfixed with “cb”, for example a transaction with an id of “11e5c89fd8f049d48baec774” would be returned as “11e5c89fd8f049d48baec774cb” “decline” (ach and cc) |
| transaction.id                  | Unique id per transaction, will be the same in the case of VOID                                                                                                                                                                                                                                                                                                                                                     |
| transaction.account_holder_name | The name of the person associated with the payment method. For CC, this is the Cardholder name. For ACH, this is the Account Owner.                                                                                                                                                                                                                                                                                 |
| transaction.account_type        | Will be one of the following values: "visa" "mc" "amex" "disc" "jcb" "diners" "checking" "savings"                                                                                                                                                                                                                                                                                                                  |
| transaction.created             | Local User timezone of created date/time (yyyy-mm-dd hh:mm:ss) the transaction was created                                                                                                                                                                                                                                                                                                                          |
| transaction.transaction_amount  | Transaction amount (will be $0.00 for a VOID as the transaction was not settled)                                                                                                                                                                                                                                                                                                                                    |
| transaction.auth_code           | 6 character authorization code                                                                                                                                                                                                                                                                                                                                                                                      |
| transaction.last_four           | Last 4 off account                                                                                                                                                                                                                                                                                                                                                                                                  |
| transaction.description         | Description of transaction                                                                                                                                                                                                                                                                                                                                                                                          |
| transaction.is_recurring        | Will be either 1 (true) or 0 (false)                                                                                                                                                                                                                                                                                                                                                                                |
| transaction.all_tags            | Comma separated list of tags applied to the transaction                                                                                                                                                                                                                                                                                                                                                             |