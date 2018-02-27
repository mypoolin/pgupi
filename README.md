# Mypoolin - WibmoPay Payment Gateway

## url: https://pgupi.mypoolin.com/payment

***

## Passing the variables

The variables below have to be passed using POST request to the url given above with the following field names.
* checksum (creation process is described below) 
* merchant_name (your merchant username)
* merchant_message (message that you want to be displayed on the page)
* merchant_txn_id (your transaction id)
* amount
* mobile (mobile number of the user)

**Please note again that these parameters have to be sent in a POST request**


## Checksum creation and usage

Checksum is a field used to validate the parameters of the transaction.
To create the checksum we will require the following fields:

1. Username (your merchant username)
2. Merchant Transaction ID (merchant_txn_id)
3. Amount to be requested (amount)
4. Secret/API Key (your merchant secret)

We create a verifying string in the following manner:

```
username|merchant_txn_id|amount|secret
```

Here we are using | (pipe) as a separator. Now we will hash this verifying string with sha512 algorithm
and pass it in the calls

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

## Psedocode For CheckSum Generation

1. Initializing array  
     arr = [username, merchant_txn_id, amount, secret]

2. Concatenate elements in array with pipe(|)  
     for (data in arr){  
     checksum_str  += str(data) + '|'  
     }
    
     checksum_str = checksum_str[:-1]

3. Generate hash512  
     return hashlib.sha512(checksum_str).hexdigest().upper()

for further queries please call: +91-7418423277 / +91-9665910027 



