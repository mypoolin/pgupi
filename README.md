# WibmoPay Payment Gateway

## Live Environment url: https://pgupi.mypoolin.com/payment
## Test Environment url: https://testpg.wibmopay.com/payment

***

This is the documentation for integrating WibmoPay payment gateway on your website. 

## Passing the parameters for opening the payment page

The variables below have to be passed using form (in html) with POST action to the url given above with the following field names. The payment page will automatically open up as soon as you submit the form (in html). 

* checksum (creation process is described below) 
* merchant_name (your merchant username eg-test-name-p2m)
* merchant_txn_id (your unique transaction id eg - 12345ABCDE)
* amount
* payer_mobile (mobile number of the user) - optional
* merchant_message (message that you want to be displayed on the page) - optional
* payer_name (Name of the user) - optional
* payer_email (Email of the user) - optional
* paymentoptions (comma separated payment mode you want eg - upi,paytm,mobikwik,cards,nb) - optional
* callbackurl (your merchant callback) - optional

**Please note again that these parameters have to be sent in a POST request.**
 The optional parameters of payer_mobile and payer_email will help skip the first 2 pages of our payment flow where we ask for these details from the consumer. 

 **Your Form will look like this**
  ~~~
  <form action="https://pgupi.mypoolin.com/payment" name="payment" method="post">
		<input type="hidden" name="checksum" value="{{checksum}}">
		<input type="hidden" name="amount" value="10">
		<input type="hidden" name="merchant_name" value="test-wibmo-p2m">
		<input type="hidden" name="merchant_txn_id" value="12345ABCDE">
		<input type="hidden" name="merchant_message" value="Test">
		<input type="hidden" name="paymentoptions" value = "paytm,mobikwik,upi,cards">
		<input type="hidden" name="payer_mobile" value="9599771577">
		<input type="hidden" name="payer_email" value="abcd@gmail.com">
		<input type="hidden" name="payer_name" value="WibmoPay">
		<input type="hidden" name="callbackurl" value="https://merchant.com/callback">
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

![#f03c15](https://placehold.it/15/f03c15/000000?text=+) NOTE: It is a must that you calculate the returning checksum and validate it against the returning checksum passed by WibmoPay Payment Gateway

 

The merchants are required to submit a redirection url to report the completion of the order. You can pass your desired redirection url by going to the merchant dashboard at https://merchants.mypoolin.com in the "my account" section change the redirection url.

## Older Version (Updated to new version below)
"checksum" is the key corresponding to old checksum.  
The returning checksum is calculated by using ```merchant_name|order_id|merchant_txn_id|status|secret``` and then creating a hash with sha512 algorithm.

## Newer Version
"return_checksum" is the key corresponding to new checksum.    
The new returning checksum is calculated by using ```merchant_name|order_id|merchant_txn_id|status|payment_mode|amount|commission|channel|secret``` and then creating a hash with sha512 algorithm.

The use of the redirection url will be done with a GET request in the following manner:

```https://sampleredirectionurl.com?merchant_name=<merchant_name>&merchant_txn_id=<MERCHANT TXN ID>&order_id<MYPOOLIN_ORDER_ID>&status=<status_of_transaction>&checksum=<old_returning_checksum>&payment_mode=<mobikwik or PPI or DC or CC or PC or UPI or NB>&amount=<amount>&commission=<commission>&channel=<ANDROID or WEB or IOS>&return_checksum=<new_returning_checksum>```


The status can be of the following types:
1. COMPLETED (If transaction is successful)
2. ABORTED (If checksum sent is invalid)
3. FAILED (for cases where there is error in creation)
4. RETRIED (for cases where same merchant_txn_id is passed)
6. DECLINED (UPI transactions when user clicks on decline on the UPI app)

![#f03c15](https://placehold.it/15/f03c15/000000?text=+) NOTE: It is a must that you calculate the returning checksum and validate it against the returning checksum passed by WibmoPay Payment Gateway

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

for further queries please reach out to the developers on backend@wibmopay.com

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

## Sample Code for Checksum in JAVA  
```Java
 static String sha1(String input) throws NoSuchAlgorithmException {
        MessageDigest mDigest = MessageDigest.getInstance("SHA-512");
        byte[] result = mDigest.digest(input.getBytes());
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < result.length; i++) {
            sb.append(Integer.toString((result[i] & 0xff) + 0x100, 16).substring(1));
        }
         
        return sb.toString();
    }
```
For testing of java checksum code please visti URL - http://tpcg.io/kc0v6z


# Check status API
#### Test url: https://testpgupi.mypoolin.com/check/status
#### Live url: https://pgupi.mypoolin.com/check/status
### Passing the parameters for checking transaction status

POST request with with following parameters

* order_id (The unique id generated by us)

     OR 
* merchant_id (Shared by us on account creation)
* merchant_txn_id (your unique transaction id eg - 12345ABCDE)

The status can be one of the following:
1. COMPLETED (If transaction is successful)
2. PENDING (when the user closes tab without doing any transaction )
3. FAILED (for cases where there is an error in creation)
4. DECLINED (for UPI transactions when the user declines transaction)
5. SETTLED (Amount is settled to your account)
6. UNKNOWN (proper POST parameters are not passed)

## Postman collection of the above APIs with examples - https://www.getpostman.com/collections/7ad1c92d77a5a7a77595

**Documentation Last Updated - 24th April 2019**
