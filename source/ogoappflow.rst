#############
OGO APP Flows
#############

The OGO App, uses a series of websocket based messages to carry out tasks such as registering a card, generating payment requests and processing them etc.

==================
Registering a Card
==================

A combination of http requests and socket messages are involved when adding a card via the OGO Client App.

* The client needs to do an http request to get a **transaction key**
  https://cs.ogo.exchange/api/transactionkey
  https://ms.ogo.exchange/api/transactionkey

* The transaction key will be included in the **AddCardRequest** socket messages
* The socket server will respond with an **AddCardResponse** 
* The **AddCardResponse** will contain 

.. code-block:: none

    orderid
    amount
    merchantid
    customerid
    returnURL
    hash

* Another request needs to be sent with the following structure

.. code-block:: none

    orderid
    amount
    merchantid
    customerid
    returnURL
    
    hash    
    cardHolderName;
    cardNumber;
    expiry;
    cvv;

* The addcard endpoint will return with a message. The message contains and html page.

* A webview should be opened up on the client phone and the html page should be loaded on it.

* Client will have to follow the screen and validate via OTP.

* Upon success, a socket message will be received with messagetype either CardAddSuccess or CardAddFail



