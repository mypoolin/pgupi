# Mypoolin - WibmoPay Payment Gateway

## Live Environment url: https://pgupi.mypoolin.com/payment
## Test Environment url: https://testpgupi.mypoolin.com/payment

***

This is the documentation for integrating WibmoPay payment gateway on your website. 

## Passing the parameters for opening the payment page

The variables below have to be passed using form (in html) with POST action to the url given above with the following field names. The payment page will automatically open up as soon as you submit the form (in html). 

* checksum (creation process is described below) 
* merchant_name (your merchant username)
* merchant_txn_id (your transaction id)
* amount
* payer_mobile (mobile number of the user) - optional
* merchant_message (message that you want to be displayed on the page) - optional
* payer_name (Name of the user) - optional
* payer_email (Email of the user) - optional
* paymentoptions (comma separated payment mode you want eg - upi,paytm,mobikwik,cards) - optional

**Please note again that these parameters have to be sent in a POST request.**
 The optional parameters of payer_mobile and payer_email will help skip the first 2 pages of our payment flow where we ask for these details from the consumer. 

 **Your Form will look like this**
  ~~~
  <form action="https://pgupi.mypoolin.com/payment" name="payment" method="post">
		<input type="hidden" name="checksum" value="{{checksum}}">
		<input type="hidden" name="amount" value="10">
		<input type="hidden" name="merchant_name" value="test-shobhit">
		<input type="hidden" name="merchant_txn_id" value="12345ABCDE">
		<input type="hidden" name="merchant_message" value="Test">
		<input type="hidden" name="paymentoptions" value = "paytm,mobikwik,upi,cards">
		<input type="hidden" name="payer_mobile" value="9599771577">
		<input type="hidden" name="payer_email" value="mypoolin@gmail.com">
		<input type="hidden" name="payer_name" value="Mypoolin">
   </form>
   ~~~



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
7. SETTLED

## Pseudocode For CheckSum Generation

You can find below the steps to be followed for generating the checksum when making the POST request. 
Also, pseudo code is written below to give you an idea. 

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

## Sample Code for checksum in Python  
```py
import hashlib  
arr = [username, merchant_txn_id, amount, secret]  
checksum_str = ''  
for data in arr:  
   checksum_str  += str(data) + '|'  

checksum_str = checksum_str[:-1]  
return hashlib.sha512(checksum_str).hexdigest().upper()  
```
## Sample Code for checksum in PHP  
```php
<?php  
$arr = array(username, merchant_txn_id, amount, secret);  
$checksum_str = '';  
foreach ($arr as $data){  
  $checksum_str  .= (string)$data . '|';  
}  

$checksum_str = substr($checksum_str, 0, -1);  
return strtoupper(hash("sha512", $checksum_str));  
?>  
```

## Sample Code for Checksum in Node JS  
```js
var crypto = require('crypto');  
var arr = [username, merchant_txn_id, amount, secret];  
var checksum_str = '';  
for(var i = 0;i<arr.length;i++){  
	checksum_str +=  arr[i] + '|';  
}  

checksum_str = checksum_str.substring(0, checksum_str.length - 1);  
return crypto.createHash('sha512').update(String(checksum_str)).digest('hex').toUpperCase();  
```



