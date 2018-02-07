# Mypoolin UPI Payment Gateway

## url: https://pgupi.mypoolin.com/


***

## Checksum creation and usage

Checksum is a field used to validate the parameters of the transaction.
To create the checksum we will require the following fields:

1. Username (username)
2. Merchant Transaction ID (merchant_txn_id)
3. Amount to be requested (amount)
4. Secret/API Key (secret)

We create a verifying string in the following manner:

```
username|merchant_txn_id|amount|secret
```

Here we are using | (pipe) as a separator. Now we will hash this verifying string with sha512 algorithm
and pass it in the calls

## Passing the variables

The variables have to be passed using POST request to the url given above with the following field names.
* checksum
* merchant_name
* merchant_message (message that you want to be displayed on the page)
* merchant_txn_id
* amount

**Please note again that these transactions have to be a POST request**

## Redirection URL

The merchants are required to submit a redirection url to report the completion of the order.

The returning checksum is calculated by using ```username|order_id|merchant_txn_id|status|secret``` and then creating a hash with sha512 algorithm.

The use of the redirection url will be done with a GET request in the following manner:

```https://sampleredirectionurl.com?merchant_name=<merchant_name>&merchant_txn_id=<MERCHANT TXN ID>&order_id=<MYPOOLIN_ORDER_ID>&status=<status_of_transaction>&returning_checksum=<returning_checksum>```


The status can be of the following types:
1. success
2. cancelled
3. pending
4. error (for cases where there is error in creation)
5. retried (for cases where same merchant_txn_id is passed)
6. declined

for further queries please call: 7418423277



