---
title: Fraudrankr API Reference

language_tabs:
  - shell

toc_footers:
 - <a href='http://www.fraudrankr.com/'>Request API Key</a>
 - <a href='https://github.com/fraudrankr/api-docs/edit/master/source/index.md'>Edit on Github</a>
---

# Introduction

> API endpoints

```shell
# transaction events (authorize, capture, sale etc.)
https://c.fraudrankr.com/transactions
# labels (chargeback, dispute etc.)
https://c.fraudrankr.com/labels
# custom events (customer service contact events etc.)
https://c.fraudrankr.com/custom-events
```

The Fraudrankr REST API supports 3 different resources (events).

A message sent to our API corresponds to a user event (transaction, label
and custom event) on your service.

Base URL: `https://c.fraudrankr.com/`

All responses will be delivered in JSON format. HTTP response codes are used to
indicate API errors.


# Quickstart

To use Fraudrankr you'll need to send us only two things:

* Your transaction details
* And you'll need to tell us about your chargebacks

This is all we need in order to identify and predict fraud and chargebacks.

## Load historic data

You are able to load historic data into Fraudrankr, this enables you to get
fraud predictions from day one.

You'll need to set the `time` parameter for all historic events (transactions, chargebacks etc.). The [Common fields](#common-fields) section outlines how to send event data to Fraudrankr.


# Testing the API

```shell
echo '{
    "apikey": "<apikey>",
    "user_id": "1234",
    "transaction_id": "ab1cd2-1234-5678",
    "label": "chargeback",
    "reason": "30"
}'
curl -X POST -d - https://c.fraudrankr.com/labels
```

>The above command will send the JSON document (in HTTP request body):

```http
POST /labels HTTP/1.1
Host: c.fraudrankr.com
```

```json
{ 
  "apikey": "<apikey>", 
  "user_id": "1234",
  "transaction_id": "ab1cd2-1234-5678",
  "label": "chargeback",
  "reason": "30"
}
```

Use your HTTP client of choice whether it is `wget`, `curl`,
[Postman](http://www.getpostman.com), or
[RESTClient](https://addons.mozilla.org/en-US/firefox/addon/restclient).
We will use `curl` for the examples so you can easily copy and paste to test.


# Authentication
FraudRankr uses API keys to allow access to the API. You can register for a
FraudRankr API key at [fraudrankr.com](http://www.fraudrankr.com/)

FraudRankr expects the API key to be included in all JSON API requests to the
server that looks like the following:

```json
{
    "apikey": "<apikey>",
    "user_id": "1234",
    "transaction_id": "ab1cd2-1234-5678",
    "label": "chargeback",
    "reason": "30"
}
```
<aside class="notice">
You must replace `<apikey>` with your own API key.
</aside>

# Common fields

Common fields for all event types:

Parameter | Required | Description
--------- | ------- | -----------
apikey | yes | The API key should be included in all API requests.
user_id | no | Unique ID for the user from your system.
user_ip | no | IP address of the user.
user_email | no | Mail address of the user.
time | no | Time (UTC) of the event since epoch (in seconds) e.g. 1392211854.

<aside class="notice">
The `time` parameter is optional, but when provided it must to be in UTC. You
must use the `time` parameter in order to load historic data into Fraudrankr.
</aside>

<aside class="notice">
The `user_mail`, `user_ip`, and `user_id` parameters are not required, but
sending them helps us to better score the users.
</aside>

# Transaction Events

> An example ``sale transaction`` event

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "user_ip": "219.231.41.48",
        "transaction_id": "ab1cd2-1234-5678",
        "label": "sale",
        "amount": 25000,
        "currency": "EUR",
        "card": {
            "bin": 411111,
            "last4": 1234,
            "name_on_card": "John Doe"
        },
        "shipping_address": {
            "address_1": "50 Narrow Street",
            "address_2": "Flat 20",
            "zipcode": "SW1A 1AA",
            "city": "London",
            "country": "GB"
        }
    }
```

> An example ``refund transaction`` event

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "transaction_id": "ab1cd2-1234-5678",
        "label": "refund",
        "amount": 25000,
        "currency": "EUR",
        "card": {
            "bin": 411111,
            "last4": 1234,
            "name_on_card": "John Doe"
        }
    }
```

### HTTP Request

`POST https://c.fraudrankr.com/transactions`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
transaction_id | yes | ID of the transaction in your system.
label | yes | Indicates the type of transaction. Valid values: (sale, authorization, capture, refund, credit, recurring)
amount | yes | The amount of the transaction (written in minor unit e.g. €20 EUR equals 2000 cents).
currency | yes | [ISO 4217 currency code](http://en.wikipedia.org/wiki/ISO_4217) e.g. EUR.
user_id | no | Unique ID for the user from your system.
**card** | no | Details on the card used to do the transaction.
bin | yes | The first 6 digits of the card number (aka the IIN number) e.g. 429503.
last4 | yes | The last 4 digits of the card number e.g. 1234
name_on_card | yes | The name of the card holder e.g. John Doe.
**shipping_address** | no | Provided if any shipping takes place.
address_1 | yes | Address line 1 e.g. 50 Narrow Street.
address_2 | no | Address line 2 e.g. Flat 20.
zipcode | yes | Zipcode e.g. SW1A 1AA.
city | yes | City e.g. London
country | yes | [ISO 3166-1_alpha-2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) two-letter country code e.g. GB.

<aside class="notice">
The `card` and `shpping_address` parameters are not required, but sending them
helps us to better score the users.
</aside>


# Label Events

> An example using the ``chargeback`` label

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "transaction_id": "ab1cd2-1234-5678",
        "label": "chargeback",
        "reason": "30",
        "time": 1371804841
    }
```

> An example using the ``retrieval-request`` label

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "transaction_id": "ab1cd2-1234-5678",
        "label": "retrieval-request",
        "reason": "28",
        "time": 1371804841
    }
```

Labels are used to mark users that our service should learn from. An
example could be marking a user that sent a chargeback (i.e.
``"label": "chargeback"``).

### HTTP Request

`POST https://c.fraudrankr.com/labels`


### Parameters

Parameter | Required | Description
--------- | ------- | -----------
transaction_id | yes | ID of the transaction in your system.
label | yes | Label of the event e.g. chargeback. See restrictions bellow.
reason | no | Card scheme reason code e.g. 30.

<aside class="notice">
Help us by using card scheme specific reasons code in ``reason``. See
[Chargeback Reason Codes](#chargeback-reason-codes) and [Retrieval Request
Reason Codes](#retrieval-request-reason-codes).
</aside>

There are a few reserved values for `label`:

- `ban` - A user abused your system or abused other users and should be marked
as bad.
- `unban` - A user was mistakenly banned in your system. Avoid our system will
treat good behaviour as bad behaviour.
- `chargeback` - A user made a transaction that caused a chargeback. Please
also send ``transaction_id`` that caused this chargeback.
- `retrieval-request` - A user caused a retrieval request.

# Custom Events

> Custom `open_ticket` event example

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "user_email": "john.doe@gmail.com",
        "type": "open_ticket",
        "text": "Hi Customer Service,\nWhy did you charge my account?",
        "time": 1371804841
     }
```
> Custom `close_ticket` event example

```json
    {
        "apikey": "<apikey>",
        "user_id": "1234",
        "user_email": "john.doe@gmail.com",
        "type": "close_ticket",
        "text": "Dear John,\nWe charged you €10 for your monthly subscription at service.com?",
        "time": 1371804841
     }
```

Custom events are used to tell the system of all kind of events, e.g. a
subscription to a service, customer service contact, a newsletter subsctription,
an unsubsctription, and so on.


# Page Events (JavaScript)

FraudRankr collects page events via JavaScript.

> Copy-and-paste the following JavaScript snippet onto your site just before `</head>`.

```html
<script type="text/javascript">
window.FraudRankr = {
  publishable_key: '<apikey>'
  user: '<user@example.com>'
};
(function() {
  function kickFraudRankr() {
    var d = document, f = d.createElement('script');
    f.type = 'text/javascript';
    f.async = true;
    f.src = (d.location.protocol == 'https:' ? 'https' : 'http') \
            + '://c.fraudrankr.com/resources/t.js';
    var s = d.getElementsByTagName('script')[0];
    s.parentNode.insertBefore(f, s);
    }
    if (a = window.attachEvent) {
      a('onload', kickFraudRankr);
    } else {
      window.addEventListener('load', kickFraudRankr, false);
    }
})();
</script>
```

<aside class="notice">
You should set the ``user`` variable to a value that can identify the user,
e.g. a user mail address or a user id from your systems. The ``user`` variable
is not required on all page events, but should be present on at least one
page event during a session.
</aside>

# Chargeback Reason Codes

## Visa

Code | Reason
-----|------
30 | Services Not Provided or Merchandise Not Received
41 | Cancelled Recurring Transaction
53 | Not as Described or Defective Merchandise
57 | Fraudulent Multiple Transactions
60 | Requested Copy Illegible
62 | Counterfeit Transaction
71 | Declined Authorization
72 | No Authorization Obtained
73 | Expired Card
74 | Late Presentment
75 | Cardholder Does Not Recognize the Transaction
76 | Incorrect Transaction Code
77 | Non Matching Acct Number
79 | Requested Transaction Information Not Received
80 | Incorrect Transaction Amount or Acct Number
81 | Fraudulent Transaction - Card Present
82 | Duplicate Processing
83 | Fraudulent Transaction - Card Not Present
85 | Credit Not Processed
86 | Paid by Other Means

## MasterCard

Code | Reason
-----|------
01 | Requested Transaction Data Not Received
02 | Requested item illegible
07 | Warning Bulletin File
08 | Requested/Required Authorization Not Obtained
12 | Account Number Not on File
31 | Transaction Amount Differs
34 | Duplicate Processing
35 | Card Not Valid or Expired
37 | Fraudulent Mail/Phone Order Transaction
40 | Fraudulent Processing of Transactions
41 | Canceled Recurring Transaction
42 | Late Presentment
47 | Exceeds Floor Limit, Not Authorized, and Fraud Transactions
49 | Questionable Merchant Activity
50 | Credit Posted as a Debit
53 | Cardholder Dispute Defective/Not as Described
54 | Cardholder Dispute-Not Elsewhere (U.S. only)
55 | Non-receipt of Merchandise
59 | Services Not Rendered
60 | Credit Not Processed
62 | Counterfeit Transaction Magnetic Stripe POS Fraud
63 | Cardholder Does Not Recognize - Potential Fraud

## Discover

Discover Network has both numeric and alpha-numeric dispute reason codes. Your
documentation may include either value.

Code | Code | Reason
-----|------|------
4750 | AL | Airline Transaction Dispute
4541 | AP | Automatic Payment Dispute
4586 | AW | Altered Amount Dispute
4751 | CA | Cash Advance Dispute
4550 | CD | Credit Posted as Card Sale
4594 | CR | Cancelled Reservation
4752 | DA | Declined Authorization
4534 | DP | Duplicate Processing
4535 | EX | Expired Card
4502 | IC | Illegible Transaction Documentation
4753 | IN | Invalid Card Number
4584 | IS | Missing Signature
4542 | LP | Late Presentation
4754 | NA | No Authorization
4554 | NC | Not Classified
4755 | RG | Non-receipts of Goods or Services
4553 | RM | Quality of Goods or Services Dispute
4555 | RN1| Additional Credit Requested
4555 | RN2| Credit Not Received
4756 | SV | Stored Value Dispute
4757 | TF | Violation of Operating Regulations
4540 | UA | Fraud Dispute


# Retrieval Request Reason Codes

## Visa

Code | Reason
-----|------
28   | Cardholder Requests Copy Bearing Signature
29   | Request for T&E Documents
30   | Cardholder Dispute - Cardholder Requests Draft
33   | Legal Process or Fraud Analysis
34   | Repeat Request for Copy

## MasterCard

Code | Reason
-----|------
05 | Chargeback Support - Card Holder Does Not Agree with Amount Billed
21 | Cardholder Inquiry - Does Not Recognize Transaction (Merchant Name, City, State or Date)
22 | Cardholder Inquiry - Disagrees With Billing
23 | Cardholder Inquiry - Needs for Personal Records
24 | Cardholder Inquiry - No Reason Code
41 | Legal/Fraud - Signature Verification
42 | Potential Chargeback or Compliance Documentation
43 | Legal/Fraud - Imprint Verification

## Discover
Discover Network has both numeric and alpha-numeric dispute reason codes. Your
documentation may include either value.

Code | Code | Reason
-----|------|------
4750 | AL   | Airline Transaction Dispute
4541 | AP   | Automatic Payment Dispute
4586 | AW   | Altered Amount Dispute
4751 | CA   | Cash Advance Dispute
4550 | CD   | Credit Posted as Card Sale
4594 | CR   | Cancelled Reservation
4534 | DP   | Duplicate Processing
4554 | NC   | Not Classified
4755 | RG   | Non-receipts of Goods or Services
4553 | RM   | Quality of Goods or Services Dispute
4555 | RN1  | Additional Credit Requested
4555 | RN2  | Credit Not Received
4540 | UA10 | Swiped Card Transaction - Proof of Valid Card Sale
4540 | UA20 | Keyed Card Transaction - Proof of Valid Card Sale
4540 | UA30 | Card Not Present Transaction - Proof of Delivery

# Errors

Error Code | Meaning
---------- | -------
400 | Bad Request -- Something is wrong with your request
401 | Unauthorized -- Your API key is wrong
403 | Forbidden
404 | Not Found
405 | Method Not Allowed
429 | Too Many Requests -- You're requesting too fast. Slown down.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.
