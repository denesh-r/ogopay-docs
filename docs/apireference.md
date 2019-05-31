# API Reference

---

## Base URLs

| Environmemt | URL                             |
| ------------| --------------------------------|
| Test        | `https://test-ipg.ogo.exchange` |
| Live        | `https://live-ipg.ogo.exchange` |

Please contact us to obtain credentials.

---

## Authentication

All transaction related endpoints require an *Authorization Header* and a *Date Header* to be set in the HTTP request.
These headers should be in the following format.  

``` http
Authorization: OGO <merchantid>:<hmac-hash>
Date: <ISO-DATE-TIME>
```

For Example:  

``` http
Authorization: OGO kangaroo:C7WInYIVNkjhFUD5MWtjqJkLX5qU+vJLDfFYSUiMSog=
Date: 2019-02-27T12:27:53.331Z
```

To calculate the hash, create a string that consists the current Date in ISO-DateTime format on the first line and the
body of the request on the second line. Lines should be separated with the new line character (*'\n'*).
This string should then be used with the provided merchant secret key to create an *HmacSHA256* hash. The hash should
then be converted to Base64 encoding. The final Base64 encoded hash, should be included in the Authorization Header.

``` js tab="JS"
var datestamp = moment().toISOString();

// the secret key provided for the merchantid
var secret = "5azaobIk7jvdFsRvuO11Ko3WkAoKEpMAz4JMvOrxkbs=";
var stringToSign = datestamp + '\n' + JSON.stringify(requestBody);

var hmac = CryptoJS.HmacSHA256(stringToSign, secret);
var hash = hmac.toString(CryptoJS.enc.Base64);
```

``` php tab="PHP"
<?php
// the secret key provided for the merchantid
$secret = "5azaobIk7jvdFsRvuO11Ko3WkAoKEpMAz4JMvOrxkbs=";

$isoDateTime = date('c');
$jsonString = json_decode($body);
$strToSign = $isoDate."\n".$jsonString;
$hmac =  hash_hmac('sha256', $strToSign, $secret, true);
$hash = base64_encode($hmac);
```

<!--
## Errors

TODO
-->

---

## Operations

---

### Get API Version

Returns the API Version

**Request**

``` http
GET /version
```

**Response**

``` json
{
  "name": "OGO Pay",
  "version": "2.0.0"
}
```

---

### Tokenize Card - HTML

Initiates card tokenization process.  
Returns an HTML response that can be used to redirect the user or show in an iframe.  
This request should be sent from the user's browser as an HTML form submission.

**Request**

``` http
POST /registercard-html
Content-Type: application/x-www-form-urlencoded
```

**Body**

``` http
cardHolderName=Mr%20Customer&
cardNumber=4242424242424242&
expiryMonth=12&
expiryYear=21&
merchantId=your-merchant-id&
customerId=your-customer-id&
orderId=an-order-id&
returnUrl=https%3A%2F%2Fbackend.myapp.com%2Fcard-tokenize-response-handler
```

**Response**

A succesfull response will have status code 200 and an html page in the content.
A failure response will either have a 400 or 500 status code and an error message in the content.

---

### Tokenize Card - JSON

Initiates card tokenization process.  
Returns an HTML result that must be shown in a webview.  
This request should be sent from the mobile app.

**Request**

``` http
POST /registercard-json
Content-Type: application/json
```

**Body**

``` json
{
  "cardHolderName": "Mr Customer",
  "cardNumber": 4242424242424242,
  "expiryMonth": 12,
  "expiryYear": 19,
  "merchantId": "your-merchant-id",
  "customerId": "your-customer-id",
  "orderId": "an-order-id",
  "returnUrl": "https://backend.myapp.com/card-tokenize-response-handler"
}
```

**Response**

``` json
{
  "success": true,
  "message": "3DSecure Page",
  "result": "<!DOCTYPE HTML PUBLIC..."
}
```

---
<!--
        RegisterCard:
          Type: Api
          Properties:
            Path: /registercard
            Method: post
        DefaultHostedPage:
          Type: Api
          Properties:
            Path: /defaulthostedpage
            Method: get
-->

### Purchase Transaction

This requests must be sent by the backend service.
An HMAC-SHA256 hash needs to be calculated using the merchant secret key.
The HTTP Authorization header must contain this hash.
Refer to [Authentication](#authentication) on how to calculate the hash.

**Request**

``` http
POST /purchase
Authorization: OGO merchantid:hmac-hash
```

**Body**

``` json
{
  "amount": 20050,
  "token": "1d75cb9494bd437ab2bbc6ed7e5c9933",
  "orderID": "100DCEFGH",
  "customerID":"123"
}
```

**Response**

``` json
{
  "success": true,
  "message": "Transaction successfull",
  "result": {
    "referenceNumber": "228680501735",
    "responseCode": "00",
    "approvalCode": "3jcPVy",
    "date": "03/13",
    "time": "13:14:21",
    "invoiceNumber": "976593",
    "stan": 922515,
    "type": 200,
    "transactionId": "3b2d9f5c-d8d3-4220-a1f8-13babe066050",
    "message": "Approved"
  }
}

```

<!-- 
        DoPurchase:
          Type: Api
          Properties:
            Path: /purchase
            Method: post
        DoSettlement:
          Type: Api
          Properties:
            Path: /settlement
            Method: post
        DoVoid:
          Type: Api
          Properties:
            Path: /void
            Method: post
        DoPreauth:
          Type: Api
          Properties:
            Path: /preauth
            Method: post
        DoCompletion:
          Type: Api
          Properties:
            Path: /completion
            Method: post 
-->
