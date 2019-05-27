# Getting started

1. First, build a [card capture form](#card-capture-form) in your application frontend.
2. Next, implement a [response handler](#response-handler) in your application backend.  
3. Finally, integrate a call to perform a [purchase transaction](#purchase-transaction) from your application backend.

## Card Capture Form

Design a card capture form in your application front end. This might be an [Android](applications.md#Usage) or iOS mobile app, an Angular/Vue/React
front end, or a PHP, JSP, ExpressJS or any other server side generated HTML website.

!!! warning "Secure your website with HTTPS"
    Please make your website is secured with a **TLS certificate**.
    Also confirm that all **non HTTPS** requests get **redirected to HTTPS**.

The typical flow of this process will be...

1. User will enter his/her card details on the form.
2. The data will be submitted to the OGO Pay service directly, securley.
3. A response will be returned with an HTML page.
4. This response needs to be shown in a webview or as a web page, depending on your application frontend.
5. The HTML page will take the user through the 3DSecure process and will eventually land at the Response Handler you built.

You will have to capture and submit the following from your user.

+ Card Holder Name (optional but preferred)
+ Card Number
+ Expiry Month
+ Expiry Year

Depending on the type of response you prefer to work with, you may either submit the details to the
[JSON endpoint](apireference.md#tokenize-card-json) or the [HTML endpoint](apireference.md#tokenize-card-html).

!!! warning "DO NOT LOG, STORE or SUBMIT card details"
    **DO NOT** under any circumstance, **LOG** captured card data, **STORE** them anywhere in your application environment
    or **SUBMIT** to any unauthorized services.

    *Card details must only be submitted to the OGO Pay service and must be directly submitted from the user's mobile device
    or browser*.

## Response Handler

An endpoint has to be implemented in your application backend that will process a **POST** request with data submitted
as a form **(application/x-www-form-urlencoded)**.

The following fields will be submitted to your endpoint.  

+ success (true or false)
+ message (*Card tokenized successfully* or an error message)
+ token (card token if successfull)
+ cardMask (masked card number in the format 4242-42xx-xxxx-4242)
+ expiryMonth (two digit month)
+ expiryYear (two digit year)
+ merchantId (the merchant id sent with the tokenize api call)
+ customerId (the customer id sent with the tokenize api call)
+ orderId (the order id sent with the tokenize api call)

It is your responsibility to **process the incoming data** and **save the token** with the relevant customer.  
You will be using this token in all future transactions related to this card.  
**Once you complete processing** the response, you have to **redirect to a different page** in your website or
in the case of a mobile app, redirect to a url that can be caught by a listener in your webview and process accordingly.

## Purchase Transaction

Send a [Purchase Transaction](apireference.md#purchase-transaction) whenever you wish to performa a transaction using a
customer's tokenized card. Initiate this transaction from your application backend. Make every effort to avoid double
submissions.  
Refer to [Authentication](apireference.md#authentication) to calculate and include a hash in your request.  
You are expected to save the responses from this api call whether successful or not. This data is important during troubleshooting.
