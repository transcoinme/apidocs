---
title: API for Merchant Transcoin.me

language_tabs: # must be one of https://git.io/vQNgJ
  - curl examples

toc_footers:
  - <a href='https://merch.transcoin.me'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to Transcoin.me API. This API  provide an opportunity to access cryptocurrency exchange and sale transactions.
This guide provides examples of queries for various purposes: 

* process - method to create merchant and exchange transactions;
* getCalcData - data acquisition method for create transactions;
* getCalcComissions - this method provides transaction calculation amount;

You can use any means to create API requests. In this guide, curl-based queries will be given as examples.


# Request Description

Request data exchange takes place in json format. So you must prepare the incoming data accordingly, and also decrypt the response.

## Method process

Using requests to the process method you can create cryptocurrency exchange transactions. For security reasons, the heading Sign 
should be added to the request method header. See the [internal link](#Signature creation)'Signature creation' section.

The type of operation must be specified in request parameter `type`. The `type` parameter can take such values: `merchant`, `exchange`.

* `merchant` - creating request for payment with cryptocurrency; 
* `exchange` - creating cryptocurrency purchase request;
* `token`    - creating request for the purchase of tokens (game currencies);
* `direct`   - creating cryptocurrency purchase and sale requests;

The remaining request parameters depend on the type of transaction you are creating.

Parameters for the request type = 'merchant':

> Example of process request type = 'merchant' 
 
```javascript
curl 'https://api.transcoin.io/v1/process/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"type" : "merchant",
	"project_id" : <your project id>,
    "amount" : 200,
    "currency" : "EUR",
    "crypto_currency" : "BTC",
    "order_number" : 24,
    "description" : "Order Payment #24",
	"autoredirect": 1,
    "success_url": '<transaction_success_url>',
    "fail_url": '<transaction_fail_url>',
    "date" : "01-06-2020 20:39:32"} \ 
```
>

| № | Parameter name  | Type   | Description                                                           |   |
|---|-----------------|--------|-----------------------------------------------------------------------|---|
| 1 | project_id      | int    | ID of your project in our system                                      |   |
| 2 | amount          | float  | Request amount                                                        |   |
| 3 | currency        | string | Currency abbreviation                                                 |   |
| 4 | crypto_currency | string | Short name for cryptocurrency                                         |   |
| 5 | order_number    | int    | The order number in your store (must be unique for a specific project)|   |
| 6 | description     | string | Payment Description                                                   |   |
| 7 | autoredirect    | int    | Can take values 0 or 1. Enables automatic redirect                    |   |
| 8 | success_url     | string | URL redirect address for transactions in success status               |   |
| 9 | fail_url        | string | URL redirect address for transactions in fail status                  |   |
| 10| date            | string | Order date in date format ('d-m-Y H: i: s')                           |   |

The method returns a response also in json format. Description of the response for a successful outcome:

> Response example
 
```javascript
{"result":true,
 "id":37,
 "wallet": <your wallet>,
 "amount":0.02989888722560715,
 "currency":"3",
 "fee":0.0014949443612803576,
 "qrcode_url": "<YOUR_API_URL>",
 "checkout_url":"<YOUR_API_URL>",
 "test":true
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | True if the request succeeds.                         |   |
| 2 | id             | int    | Transaction ID in our system                          |   |
| 3 | wallet         | string | Your wallet                                           |   |
| 4 | amount         | float  | Cryptocurrency Amount                                 |   |
| 5 | currency       | int    | Cryptocurrency identifier in our system               |   |
| 6 | fee            | float  | Amount of commission (in cryptocurrency)              |   |
| 7 | qrcode_url     | string | QR code URL                                           |   |
| 8 | checkout_url   | string | Payment URL                                           |   |
| 9 | test           | bool   | Sign of a test order. If true, the order is test.     |   |

In case of a negative result, responce will be as follows:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |


Description request type='exchange':

> Request type='exchange' example
 
```javascript
curl 'https://api.transcoin.io/v1/process/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"type":"exchange",
	"method":"2",
	"amount":"100",
	"from":"2",
	"to":"3",
	"wallet":<your wallet>,
	"email":"your_email@example.com",
	"autoredirect": 1,
    "success_url": '<transaction_success_url>',
    "fail_url": '<transaction_fail_url>',
	"partner_id":<your ID> } \ 
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | partner_id     | int    | Partner ID in our system (required)                     |   |
| 2 | wallet         | string | Wallet to send cryptocurrency                           |   |
| 3 | email          | string | Customer Email                                          |   |
| 4 | method         | int    | ID of the payment method in our system                  |   |
| 5 | from           | int    | Currency identifier in our system                       |   |
| 6 | to             | int    | Cryptocurrency identifier in our system                 |   |
| 7 | amount         | float  | Transaction Amount (in EUR or USD)                      |   |
| 9 | autoredirect   | int    | Can take values 0 or 1. Enables automatic redirect      |   |
| 10| success_url    | string | URL redirect address for transactions in success status |   |
| 11| fail_url       | string | URL redirect address for transactions in fail status    |   |
| 12| test           | bool   | Sign of a test order. If true, the order is test.       |   |

The method returns a response also in json format. Description of the response for a successful outcome:

> Successfull response type='exchange' example
 
```javascript
{ 
	"result":true,
	"exchange_data":{	
		"request_id":<request ID>,
		"partner_id":<your ID>,
		"user_id":<user's ID>,
		"from":2,
		"to":3,
		"method":2,
		"sum":200,
		"wallet":<your wallet>,
		"id":19
	},
	“request_url”: "<your API URL>/<your ID>/<request ID>/",
	"test":true
}
```
>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                 |   |
| 2 | exchange_data  | array  | array with exchange data                              |   |
| 3 | id             | int    | Transaction ID of your exchange in our system         |   |
| 4 | request_id     | string | ID of your exchange request in our system             |   |
| 5 | partner_id     | int    | Your partner ID in our system                         |   |
| 6 | user_id        | int    | Customer  ID in our system                            |   |
| 7 | method         | int    | ID of the payment method in our system                |   |
| 8 | from           | int    | Currency identifier in our system                     |   |
| 9 | to             | int    | Cryptocurrency identifier in our system               |   |
|10 | sum            | float  | Transaction Amount (in EUR or USD)                    |   |
|11 | wallet         | string | Wallet to send cryptocurrency                         |   |
|12 | request_url    | string | Request URL                                           |   |

The structure of the negative response is identical to that described above.

Description request type='token':

> Request type='token' example
 
```javascript
curl 'https://api.transcoin.io/v1/process/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"type":"token",
	"partner_id" : "<your partner id>", 
    "amount" : 200, 
	"pay_type" : "card", 
    "from" : "2", 
	"email" : "<user email here>" 
    "wallet" : "<your wallet here>", 
	"autoredirect": 1,
    "success_url": '<transaction_success_url>',
    "fail_url": '<transaction_fail_url>',
	"lang_code" : "en" } \
```
>

| № | Parameter name  | Type   | Description                                                           |   |
|---|-----------------|--------|-----------------------------------------------------------------------|---|
| 1 | partner_id      | int    | Your partner ID in our system                                         |   |
| 2 | amount          | float  | Request amount                                                        |   |
| 3 | pay_type        | string | Optional parameter, values "card" or "bank". The default is "card"    |   |
| 4 | from   	      | int    | Currency identifier in our system                                     |   |
| 5 | email           | string | User email															   |   |
| 6 | wallet          | string | User wallet                                                           |   |
| 7 | lang_code       | string | Language code in ISO format  (en,ru,lv,ee)                            |   |
| 8 | autoredirect    | int    | Can take values 0 or 1. Enables automatic redirect                    |   |
| 9 | success_url     | string | URL redirect address for transactions in success status               |   |
| 10| fail_url        | string | URL redirect address for transactions in fail status                  |   |


The method returns a response also in json format. Description of the response for a successful outcome:

> Successfull response type='token' example
 
```javascript
{ 
	“result”: “true” 
	“request_url”: "<your API URL>/<your partner ID>/<request ID>/",
	"token_data":{
		"id":30,
		"request_id":"Cfh9EiIa3T1icutc",
		"partner_id":6943,
		"user_id":"23752",
		"created":"2020-08-05 14:01:46",
		"tkn_name":"<name of token>",
		"tkn_price":"<token price>",
		"sum":100,
		"method":2,
		"tkn_sum":0,
		"to":"2",
		"from":"2",
		"wallet":"your wallet here",
		"status":0,
		"order_url":"\/<request ID>\/",
		"test":true
	}
}
```

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                 |   |
| 2 | token_data     | array  | array with exchange data                              |   |
| 3 | id             | int    | Transaction ID of your token exchange in our system   |   |
| 4 | request_id     | string | ID of your exchange request in our system             |   |
| 5 | partner_id     | int    | Your partner ID in our system                         |   |
| 6 | user_id        | int    | Customer  ID in our system                            |   |
| 7 | created        | date   | Date of creating token transaction                    |   |
| 8 | tkn_name       | string | Name of your token                                    |   |
| 9 | tkn_price      | float  | Token price                                           |   |
|10 | sum            | float  | Transaction Amount (in EUR or USD)                    |   |
|11 | method         | int    | ID of the payment method in our system                |   |
|12 | tkn_sum        | float  | Number of tokens to be purchased                      |   |
|13 | from           | int    | Currency identifier in our system                     |   |
|14 | to             | int    | Currency identifier in our system                     |   |
|15 | wallet         | string | Wallet to send cryptocurrency                         |   |
|16 | status         | int    | ID of transaction status                              |   |
|17 | order_url      | string | Order URL                                             |   |
|18 | test           | bool   | Sign of a test order. If true, the order is test.     |   |

In case of a negative result, responce will be as follows:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |

Description request type='direct':

This type of request allows you to create transactions not only for the purchase but also for the sale of cryptocurrency.
In both cases, the structure of requests is identical, only currencies and amounts are swapped.

> Request type='direct' buy example
 
```javascript
curl 'https://api.transcoin.io/v1/process/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"type":"direct",
	"partner_id":<your ID>, 
	"wallet":<your wallet>,
	"from":"2",
	"to":"3",
	"amount":"100",
	"autoredirect": 1,
    "success_url": '<transaction_success_url>',
    "fail_url": '<transaction_fail_url>'} \
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | partner_id     | int    | Partner ID in our system (required)                     |   |
| 2 | wallet         | string | Wallet to send cryptocurrency (required, only for buy)  |   |
| 3 | from           | int    | Currency identifier in our system (required)            |   |
| 4 | to             | int    | Cryptocurrency identifier in our system (required)      |   |
| 5 | amount         | float  | Transaction Amount (in EUR or USD)                      |   |
| 6 | receive        | float  | Transaction reciving sum (in crypto currency)           |   |
| 7 | autoredirect   | int    | Can take values 0 or 1. Enables automatic redirect      |   |
| 8 | success_url    | string | URL redirect address for transactions in success status |   |
| 9 | fail_url       | string | URL redirect address for transactions in fail status    |   |

> Request type='direct' sale example
 
```javascript
curl 'https://api.transcoin.io/v1/process/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"type":"direct",
	"partner_id":<your ID>, 
	"from":"3",
	"to":"2",
	"receive":"100",
	"autoredirect": 1,
    "success_url": '<transaction_success_url>',
    "fail_url": '<transaction_fail_url>'} \
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | partner_id     | int    | Partner ID in our system (required)                     |   |
| 2 | from           | int    | Currency identifier in our system (required)            |   |
| 3 | to             | int    | Cryptocurrency identifier in our system (required)      |   |
| 4 | amount         | float  | Transaction Amount (in crypto currency)                 |   |
| 5 | receive        | float  | Transaction reciving sum (in EUR or USD)                |   |
| 6 | autoredirect   | int    | Can take values 0 or 1. Enables automatic redirect      |   |
| 7 | success_url    | string | URL redirect address for transactions in success status |   |
| 8 | fail_url       | string | URL redirect address for transactions in fail status    |   |

To successfully create a transaction, you must specify one of the fields 'amount' or 'receive',
if both are specified, priority will be given to 'amount'. if both are not transmitted or equal to zero you will get an error.

The method returns a response also in json format. Description of the response for a successful outcome:

> Successfull response type='direct' example
 
```javascript
{
	"result":true,
	"exchange_id":22698,
	"data":{
		"sum_from":"0.03157722",
		"sum_with_com":"0.02990336",
		"com_valut_from":"0.0001",
		"pay_methods_com":"0",
		"sum_pay_methods_com":"0.00000000",
		"our_com":5,
		"sum_our_com":"0.00157386",
		"valut_from":"BTC",
		"metod_name":"Bank transfer(SEPA)",
		"rate":6688.21216,
		"sum_to":"200.00",
		"sum_to_with_com":"200.00",
		"com_valut_to":"0",
		"valut_to":"EUR",
		"reserv":"100000.00 EUR",
		"pay_method_min":"50",
		"pay_method_max":"20000",
		"pay_method_lim_day":"30000",
		"pay_method_lim_month":"80000",
		"pay_method_lim_day_count":"40"
	}
}
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                   |   |
| 2 | exchange_id    | int    | ID of new exchange transaction in our system            |   |
| 3 | data           | array  | array with exchange and comissions data:                |   |
|   |                | float  | sum_from - transaction Amount                           |   |
|   |                | float  | sum_with_com - transaction Amount minus comissions      |   |
|   |                | float  | com_valut_from - sum of valut from comission            |   |
|   |                | float  | pay_methods_com - comission of pay method (%)           |   |
|   |                | float  | sum_pay_methods_com - sum comission of pay method       |   |
|   |                | float  | our_com - comission of transcoin (%)                    |   |
|   |                | float  | sum_our_com - sum comission of transcoin                |   |
|   |                | string | valut_from - valut from name                            |   |
|   |                | string | metod_name - pay method name                            |   |
|   |                | float  | rate - exchange rate                                    |   |
|   |                | float  | sum_to - amount you will receive after the exchange     |   |
|   |                | float  | sum_to_with_com - amount you will receive after the exchange minus commission    |   |
|   |                | float  | com_valut_to - sum of valut to comission                |   |
|   |                | string | valut_to - valut to name                                |   |
|   |                | float  | reserv - the amount in the output currency held in reserve |   |
|   |                | int    | pay_method_min - minimum transaction amount (in EUR)    |   |
|   |                | int    | pay_method_max - maximum transaction amount (in EUR)    |   |
|   |                | int    | pay_method_lim_day - daily limit amount (in EUR)        |   |
|   |                | int    | pay_method_lim_month - monthly limit amount (in EUR)    |   |
|   |                | int    | pay_method_lim_day_count - transaction limit per day    |   |

The structure of the negative response is identical to that described above.

## Method merchant 

> Example of merchant method 
 
```javascript
curl 'https://api.transcoin.io/v1/merchant/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"project_id" : <your project id>,
	"transaction_id" : <transaction ID>} \ 
```
>

The method allows you to track the statuses of transactions. This method require 
your project_id and transaction_id as input data and returns response a string in json format.

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | project_id     | int    | ID of your project in our system (required)           |   |
| 2 | transaction_id | int    | ID of transaction to track status (required)          |   |

> Response of merchant method example 

```javascript
{ 	
	"result":true,
	"data":{
		"id":"63",
		"status":"new"
	},
	"test":true
}
```
>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                 |   |
| 2 | data           | array  | Array with response data. Such structure              |   |
| 3 | id             | int    | ID of transaction                                     |   |
| 4 | status         | string | Status. May be "new","pending","success","cancel"     |   |
| 5 | test           | bool   | Sign of a test order. If true, the order is test.     |   |


If the result is negative, the method will return a responce with the following structure:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |


## Method exchange 

> Example of exchange method 
 
```javascript
curl 'https://api.transcoin.io/v1/exchange/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"partner_id" : <your ID>,
	"exchange_id" : <exchange ID>} \ 
```
>

Almost completely similar to the merchant method except for the input data. Also allows you 
to track the statuses of exchange transactions. This method require your partner_id and exchange_id 
as input data and returns response a string in json format.

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | partner_id     | int    | Your ID in our system (required)                      |   |
| 2 | exchange_id    | int    | ID of exchange to track status (required)             |   |

> Response of exchange method example 

```javascript
{ 	
	"result":true,
	"data":{
		"id":"22553",
		"status":"pending"
	},
	"test":true
}
```
>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                 |   |
| 2 | data           | array  | Array with response data. Such structure              |   |
| 3 | id             | int    | ID of exchange                                        |   |
| 4 | status         | string | Status. May be "new","pending","success","cancel"     |   |
| 5 | test           | bool   | Sign of a test order. If true, the order is test.     |   |


If the result is negative, the method will return a responce with the following structure:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |


## Method getCalcData 

> Request getCalcData example
 
```javascript
curl 'https://api.transcoin.io/v1/getCalcData' \
  -H 'Content-Type: application/json' \
  --data { "partner_id":<your ID> } \ 
 
```
>

Method provides data for the preliminary calculation of the transaction (payment amounts and commissions).
This method require your partner_id as input data and returns response a string in json format.
 
Method response returns a fairly large array of data:

> Responce getCalcData example 

```javascript
{
   "result":true,
	"buy_payment_methods":[
		{
		    "id":"2",
			"name":"Bank Card",
			"com":"3.55",
			"our_com":"2",
			"min":"50",
			"lim":"3000",
			"enable_mes":"1",
			"lim_day":"25000",
			"lim_month":"50000",
			"lim_count":"10"
		}
      ],
    "valuts":[
        {
            "id":"1",
            "name":"USD",
            "com":"0",
            "number_format":"2",
            "reserv":"100"
        }
      ],
    "crypto_valuts":[
        {
            "id":"3",
            "name":"BTC",
            "com":"0.001",
            "number_format":"5",
            "reserv":"74.810473486663"
        }
      ],
    "comissions":{
        "sum_res_nocom":0,
        "sum":0,
        "sum_res":0,
        "pay_methods_com":"3.55",
        "our_com":"2",
        "valut_com":0.0001,
        "metod_name":"Bank Card",
        "reserv":"74.81BTC",
        "minmax":"50 - 3000",
        "min":"50",
        "max":"3000"
    }
}
```
>

| № | Parameter name       | Type  | Description                                                                           |   |
|---|----------------------|-------|---------------------------------------------------------------------------------------|---|
| 1 | result               | bool  | Always true.                                                           			   |   |
| 2 | buy_payment_methods  | array | Array with data of payment methods.                                                   |   |
|   |                      |       | An array has the following element structure:                                         |   |
|   |                      |       | id - method identifier in our system;                                                 |   |
|   |                      |       | name - name of the method;                                                            |   |
|   |                      |       | com - method commission amount;                                                       |   |
|   |                      |       | our_com - сумма комиссии за транзакцию;                                               |   |
|   |                      |       | min - the minimum transaction amount;                                                 |   |
|   |                      |       | lim - maximum transaction amount;                                                     |   |
|   |                      |       | enable_mes - whether the method is allowed;                                           |   |
|   |                      |       | lim_day - limit the amount of payments per day;                                       |   |
|   |                      |       | lim_month - monthly payment limit;                                                    |   |
|   |                      |       | lim_count - limit transactions count per day;                                         |   |
| 3 | sell_payment_methods | array | Completely similar to buy_payment_methods                                             |   |
| 4 | valuts               | array | An array of currencies. An array has the following element structure:                 |   |
|   |                      |       | id - currency identifier in our system;                                               |   |
|   |                      |       | name - currency name;                                                                 |   |
|   |                      |       | com - commission for operations in this currency;                                     |   |
|   |                      |       | number_format - the number of digits after the decimal point;                         |   |
|   |                      |       | reserv - available quantity of cryptocurrency;                                        |   |
| 5 | crypto_valuts        | array | An array of cryptocurrencies. The structure is similar to the structure of valuts     |   |
 
## Method getCalcComissions

The getCalcComissions method allows you to calculate transaction fees. Method expects as
parameters json string with the following fields:

> Request getCalcComissions example 

```javascript
curl 'https://api.transcoin.io/v1/getCalcComissions/' \
  -H 'Content-Type: application/json' \
  --data {"from":"2","to":"3","method":"21","amount":"200","partner_id":<your ID>}\
```
>
  
| № | Parameter name | Type  | Description                                |   |
|---|----------------|-------|--------------------------------------------|---|
| 1 | from   		 | int   | currency identifier in our system          |   |
| 2 | to     		 | int   | cryptocurrency identifier in our system    |   |
| 3 | method 		 | int   | method identifier in our system            |   |
| 4 | amount 		 | float | Transaction Amount (may be zero)           |   |  
| 5 | partner_id     | int   | Partner ID in our system (required)        |   |
 
You will receive the answer as follows:

> Responce getCalcComissions example 

```javascript
{ 	"result":true,
	"data":{
		"metod_name":"Skrill",
		"min":50,
		"max":3000,
		"minmax":"50 - 3000",
		"our_com":3.5,
		"pay_methods_com":1.76,
		"reserv":"74.81 BTC",
		"sum":473.69999999999993,
		"sum_res":0.07431916,
		"sum_res_nocom":0.07441915846815432,
		"valut_com":0.0001
	} 
```
>

| № | Parameter name | Type  | Description                                                                    |   |
|---|----------------|-------|--------------------------------------------------------------------------------|---|
| 1 | result 		 | bool  | true (in case of positive result)                                              |   |
| 2 | data    		 | array | Array with commission calculation data. The array has the following structure: |   |
|   |        		 | float | sum_res_nocom - amount in cryptocurrency, excluding commission;                |   |
|   |        		 | float | sum - amount of commission;                                                    |   |
|   |        		 | float | sum_res - the total amount in cryptocurrency;                                  |   |
|   |        		 | int   | pay_methods_com - payment method commission (%);                               |   |
|   |        		 | int   | our_com - transaction fee;                                                     |   |
|   |        		 | float | valut_com - conversion fee;                                                    |   |
|   |        		 | string| metod_name - name of the payment method;                                       |   |
|   |        		 | string| minmax - a line of limits in the form “<minimum value> - <maximum value>”;     |   |
|   |        		 | int   | min - the minimum transaction amount;                                          |   |
|   |        		 | int   | max - maximum transaction amount;                                              |   |
|   |        		 | float | reserv - reserve cryptocurrency available.                                     |   | 
 
 
If the result is negative, the method will return a responce with the following structure:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |
 
## Method calcDirectComissions

The calcDirectComissions method allows you to calculate direct exchange transaction fees. 
Requests are completely similar to requests of the 'process' method type 'direct'. The method calculates commissions for both buying and selling cryptocurrency.
Method expects as parameters json string with the following fields:

> Request calcDirectComissions example

```javascript
curl 'https://api.transcoin.io/v1/calcDirectComissions/' \
  -H 'Content-Type: application/json' \
  -H 'Sign: a70e6e5388f23ff6a6da503a82807f3b' \
  --data {
	"partner_id":<your ID>, 
	"from":"3",
	"to":"2",
	"amount":"0",
	"receive":"200" } \
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | partner_id     | int    | Partner ID in our system (required)                     |   |
| 3 | from           | int    | Currency identifier in our system (required)            |   |
| 4 | to             | int    | Cryptocurrency identifier in our system (required)      |   |
| 5 | amount         | float  | Transaction Amount (in EUR or USD)                      |   |
| 6 | receive        | float  | Transaction reciving sum (in crypto currency)           |   |

Fields 'amount' or 'receive' can be empty or omitted, if both are specified, priority will be given to 'amount'. 
As a result, you will receive a response similar to the response of  method 'process' request type 'direct'

> Successfull response type='direct' example
 
```javascript
{
	"result":true,
	"data":{
		"sum_from":"0.03157722",
		"sum_with_com":"0.02990336",
		"com_valut_from":"0.0001",
		"pay_methods_com":"0",
		"sum_pay_methods_com":"0.00000000",
		"our_com":5,
		"sum_our_com":"0.00157386",
		"valut_from":"BTC",
		"metod_name":"Bank transfer(SEPA)",
		"rate":6688.21216,
		"sum_to":"200.00",
		"sum_to_with_com":"200.00",
		"com_valut_to":"0",
		"valut_to":"EUR",
		"reserv":"100000.00 EUR",
		"pay_method_min":"50",
		"pay_method_max":"20000",
		"pay_method_lim_day":"30000",
		"pay_method_lim_month":"80000",
		"pay_method_lim_day_count":"40"
	}
}
```
>

| № | Parameter name | Type   | Description                                             |   |
|---|----------------|--------|---------------------------------------------------------|---|
| 1 | result         | bool   | Accepts true if the request succeeds.                   |   |
| 2 | data           | array  | array with exchange and comissions data:                |   |
|   |                | float  | sum_from - transaction Amount                           |   |
|   |                | float  | sum_with_com - transaction Amount minus comissions      |   |
|   |                | float  | com_valut_from - sum of valut from comission            |   |
|   |                | float  | pay_methods_com - comission of pay method (%)           |   |
|   |                | float  | sum_pay_methods_com - sum comission of pay method       |   |
|   |                | float  | our_com - comission of transcoin (%)                    |   |
|   |                | float  | sum_our_com - sum comission of transcoin                |   |
|   |                | string | valut_from - valut from name                            |   |
|   |                | string | metod_name - pay method name                            |   |
|   |                | float  | rate - exchange rate                                    |   |
|   |                | float  | sum_to - amount you will receive after the exchange     |   |
|   |                | float  | sum_to_with_com - amount you will receive after the exchange minus commission    |   |
|   |                | float  | com_valut_to - sum of valut to comission                |   |
|   |                | string | valut_to - valut to name                                |   |
|   |                | float  | reserv - the amount in the output currency held in reserve |   |
|   |                | int    | pay_method_min - minimum transaction amount (in EUR)    |   |
|   |                | int    | pay_method_max - maximum transaction amount (in EUR)    |   |
|   |                | int    | pay_method_lim_day - daily limit amount (in EUR)        |   |
|   |                | int    | pay_method_lim_month - monthly limit amount (in EUR)    |   |
|   |                | int    | pay_method_lim_day_count - transaction limit per day    |   |

The structure of the negative response is identical to that described above.
 
If the result is negative, the method will return a responce with the following structure:

> Negative response example
 
```javascript
{"result":false,
 "error":401,
 "message":"Wrong input data",
 }
```

>

| № | Parameter name | Type   | Description                                           |   |
|---|----------------|--------|-------------------------------------------------------|---|
| 1 | result         | bool   | Accepts false if errors occurred during execution.    |   |
| 2 | error          | int    | Error Code (optional)                                 |   |
| 3 | message        | string | Error message                                         |   |

# Signature creation

For security reasons, the heading Sign should be added to the request method header.
The sign is formed by receiving the MD5 hash of your API key and json request string:

`sign = md5($json_request_raw.$your_api_key);`

For `merchant` transaction you need your 'project api key', for `exchange`,`token`,`direct` transaction
you need your 'partner api key'. You can find it in the settings section in our site.

# Webhooks

To track status changes for your transactions, our system can send you a webhook.
To receive webhooks you need to fill in the fields callback on project settings page or
api_url_post on your exchange partner settings page. You must enter there the full URL address 
to which the webhook will be sent.

> Request body structure example

```javascript
{
	"id":"22430",
	"status":"failed",
	"type":"exchange"
	"message":""

}
```

>

| № | Parameter name | Type   | Description                                                          |   |
|---|----------------|--------|----------------------------------------------------------------------|---|
| 1 | id             | int    | Transaction ID in our system                                         |   |
| 2 | status         | string | Transaction status at WHICH status of the transaction has changed    |   |
| 3 | type           | string | Transaction type can take the following values: exchange,merchant    |   |
| 3 | message        | string | Optional. Change status message                                      |   |

Each web hook has a signature in the header. See section 'Signature creation'.