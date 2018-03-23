# Mypoolin - WibmoPay Payment Gateway

## Live Environment url: https://pgupi.mypoolin.com/payment
## Test Environment url: https://testpgupi.mypoolin.com/payment

***

This is the documentation for integrating WibmoPay payment gateway on your website. 

## Passing the parameters for opening the payment page

The variables below have to be passed using POST request to the url given above with the following field names. The payment page will automatically open up as soon as you POST these on the above url. 

* checksum (creation process is described below) 
* merchant_name (your merchant username)
* merchant_txn_id (your transaction id)
* amount
* merchant_message (message that you want to be displayed on the page) - optional
* payer_mobile (mobile number of the user) - optional
* payer_name (Name of the user) - optional
* payer_email (Email of the user) - optional
* paymentoptions (comma separated payment mode you want eg - upi,paytm,mobikwik) - optional

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

The merchants are required to submit a redirection url to report the completion of the order. You can share your desired redirection url with our team on email - admin@mypoolin.com and we will configure it for you. 

The returning checksum is calculated by using ```username|order_id|merchant_txn_id|status|secret``` and then creating a hash with sha512 algorithm.

The use of the redirection url will be done with a GET request in the following manner:

```https://sampleredirectionurl.com?merchant_name=<merchant_name>&merchant_txn_id=<MERCHANT TXN ID>&order_id<MYPOOLIN_ORDER_ID>&status=<status_of_transaction>&checksum=<returning_checksum>&payment_mode=<PAYMENT MODE>&amount=<amount>&commission=<commission>&channel=<ANDROID or WEB or IOS>```


The status can be of the following types:
1. COMPLETED
2. ABORTED
3. PENDING
4. FAILED (for cases where there is error in creation)
5. RETRIED (for cases where same merchant_txn_id is passed)
6. DECLINED

## Pseudocode For CheckSum Generation

You can find below the steps to be followed for generating the checksum when making the POST request. 
Also, pseudo code is written below in Python to give you an idea. 

1. Initialize an array and put in elements as 
     arr = [username, merchant_txn_id, amount, secret]

2. Concatenate elements in array with pipe(|)  
     for (data in arr){  
     checksum_str  += str(data) + '|'  
     }
    
     checksum_str = checksum_str[:-1]

3. Generate hash512  
     return hashlib.sha512(checksum_str).hexdigest().upper()

for further queries please call the developers on : +91-7418423277 / +91-9665910027 



