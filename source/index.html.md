---
title: 3DS MPI - API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.placetopay.com/web/'>Sign Up for a Key</a>

includes:
    - eci_codes
    - errors

search: true
---

# Introduction

Welcome to the 3DS-MPI API! You can use our API to access 3DS-MPI API endpoints, which can get you started with 3DS authentication and other resources in the MPI.

We have language bindings in Shell, You can view examples in the dark area to the right.

# Authentication
> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl POST "https://3ds-mpi.placetopay.com"
  -H "Authorization: Bearer YourApiKeyHere"
```

3DS-MPI uses API keys to authenticate requests.

3DS-MPI expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer YourApiKeyHere`

<aside class="notice">
You must replace <code>YourApiKeyHere</code> with your personal API key.
</aside>

# Making Requests

The first thing you need to know is what API URL endpoint to use:

- MPI URL: use [https://3ds-mpi.placetopay.com](https://3ds-mpi.placetopay.com)

When you write your own 3DS-MPI client, please keep the following in mind:

- Always set the __Accept__ header to `application/json`.

- Always set the __Content-Type__ header to `application/json`. 

- Always set the __Authorization__ header to `Bearer YourApiKeyHere`. 

- Client libraries will usually set these headers automatically.

# LookUpRequest

The _LookUpRequest_ is responsible for initiating user authentication. 

You must use the _LookUpRequest_ right after you have captured the payment information, which must contain the final amount of the transaction and the credit card information.

The response message returned by the MPI contains information about the enrollment of the card in 3DS.

## LookupRequest Object

> Example of **Basic** LookupRequest Object:

```json
{
    "pan": 4111111111111111,
    "expiration_year": 2029,
    "expiration_month": 4,
    "amount": 23999.69,
    "currency": "USD",
    "redirect_uri": "https://www.merchanturl.com"
}
```

> Example of **Advanced** LookupRequest Object:

```json
{
    "pan": 4111111111111111,
    "expiration_year": 2029,
    "expiration_month": 4,
    "amount": 23999.69,
    "currency": "USD",
    "redirect_uri": "https://www.merchanturl.com",
    "installments": 3,
    "recurrence": true,
    "recurrence_frequency": 20,
    "recurrence_expiration": "20201122",
    "locale": "en",
    "disable_redirect": false
}
```

The object contains information about the payment that is being processed, the information is required in order to get information about the credit card in 3DS.

### Arguments

Parameter | Type | Required | Description  
--------- | ------- | ----------- | ----------
pan | integer | true | Credit card number 
expiration_year | integer | true | Card's expiration year YYYY
expiration_month | integer | true | Card's expiration month MM
amount | number | true | The amount of the transaction
currency | string | true | ISO 4217 alpha code of the currency
redirect_uri | string | true | Return url 
installments | number | false | Payment installments 
recurrence | boolean | false | Is recurrence enabled? 
recurrence_frequency | boolean | false | Days between recurrence
recurrence_expiration | string | false | Expiration date of recurrence
locale | string | false | Localization language
disable_redirect | boolean | false | Disable MPI Redirect Handling

#### Notes

- `redirect_uri` Consumer Url, the user will return to this url once the authentication process is finished.

- `installments` If applicable, the amount of installments selected for payment.

- `recurrence` If applicable, is recurrence enabled for the payment?

- `recurrence_frequency` required if recurrence is enabled, the amount of days between each recurrence.

- `recurrence_expiration` required if recurrence is enabled, format must be YYYYMMDD.

- `locale` Localization language to be used in messages, choose from `es|en`.

- `disable_redirect` if `true`,  the MPI will not handle the automatic redirection to the ACS. The API will return the necessary data for the consumer to make the post form.

## LookupResponse Object

> Example of LookupResponse Object:

```json
{
    "enrolled": "Y",
    "redirect_url": "https://3ds-mpi.placetopay.com/authenticate/veryLargeUrl",
    "transaction_id": 1018
}
```

> Example Response (200), with `disable_redirect` as `true`

```json
{
    "enrolled": "Y",
    "acs_url": "https://www.example-acs-url.com/path/auth",
    "pa_req": "veryLargeCompressedPaReqToBeSent",
    "term_url": "http://www.exmaple-mpi-url.com/authenticate/callback/veryLargeUrl",
    "md": "merchantExtraData",
    "transaction_id": 1242
}
```

### Arguments

Parameter | Type  | Description  
--------- | ------- | ----------- | ----------
enrolled | string  | Credit card enrollment status.
redirect_url | string | MPI Url, user must visit to continue with 3DS authentication.
transaction_id | integer | MPI transaction Id.
eci_flag | number | Given ECI flag for the transaction.
acs_url | string  | if if `disable_redirect` was `true`. The original ACS Url to continue with 3DS authentication.
pa_req | string | if `disable_redirect` was `true`. Compressed payer authentication request. 
term_url | string | if `disable_redirect` was `true`. Terminal Url, a MPI url.
md | string | if `disable_redirect` was `true`. Merchant data, additional data.

#### Notes

- `enrolled` Enrollment status one of `Y|N|U`. If `Y` the user can be authenticated.

    - `Y` Authentication is available.

    - `N` Authentication is not available.

    - `U` Unable to know if authentication is available.

- `redirect_url` Present if `enrolled` is `Y`, MPI Url that handles the required action for the user to continue with the authentication process.

- `transaction_id` MPI transaction identifier.

- `eci_flag` Present if `enrolled` is `N` or `U`, The *Electronic Commerce Indicator* indicates the level of security and the result of the 3D Secure authorization. See [Eci Codes](#eci-codes)

- `acs_url` Present if `disable_redirect` was `true` & `enrolled` is `Y`. Contains the URL of the card issuer authentication site (bank).

- `pa_req` Present if `disable_redirect` was `true` & `enrolled` is `Y`. Contains information that must be posted with the authentication form.

- `term_url` Present if `disable_redirect` was `true` & `enrolled` is `Y`, Contains information that must be posted with the authentication form. 

- `md` Present if `disable_redirect` was `true` & `enrolled` is `Y`, Contains information that must be posted with the authentication form. 

## LookupRequest

> Example Request:

```shell
curl "https://3ds-mpi.placetopay.com/api/lookup" \ 
    -X POST \
    -H "accept: application/json" \ 
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer yourApiKey" \ 
    -d {"pan":4111111111111111,"expiration_year":2029,"expiration_month":4,"amount":23999.69,"currency":"USD","redirect_uri":"https://www.merchanturl.com","installments":3,"recurrence":true,"recurrence_frequency":20,"recurrence_expiration":"20201122","locale":"en","disable_redirect":false}
```

> Example Response (200):

```json
{
    "enrolled": "Y",
    "redirect_url": "https://3ds-mpi.placetopay.com/authenticate/veryLargeUrl",
    "transaction_id": 1018
}
```

LookUp credit card information in 3DS.

`POST http://example.com/api/lookup`

see [LookupRequest Object](#lookuprequest-object)

## Handle LookupResponse

Verify the `enrolled` parameter in `LookupResponse` and proceed as:

### Authentication not available, `enrolled` is`U`

The enrollment of the card in 3DS could not be verified. It can happen due to some problem in the internal systems of 3DS.

You can proceed without 3DS.

### Authentication not available, `enrolled` is`N`

The card is not enrolled in 3DS.

You can proceed without 3DS.

### Authentication is available, `enrolled` is`Y`

The card is enrolled in 3DS and the cardholder can be authenticated.

The card holder must be redirected to the url given in the `LookupResponse` as `redirect_url`. The MPI will handle the required actions
in order to continue with the authentication process 

> Example Post Form when `disable_redirect` is `true`.

```html
    <form method="POST" action="{{ $response['acs_url'] }}">
        <input type="hidden" name="PaReq" value="{{ $response['pa_req'] }}">
        <input type="hidden" name="TermUrl" value="{{ $response['term_url'] }}">
        <input type="hidden" name="MD" value="{{ $response['md'] }}">

        <noscript>
            <div>
                <h2>JavaScript is currently disabled or is not supported by your browser</h2>
                <p>Please continue with the process manually</p>
                <button type="submit" class="button">Continue'</button>
            </div>
        </noscript>
    </form>
```

<aside class="notice">
    If you used <code>disable_redirect</code> parameter as <code>true</code> then you have to handle the post form as follows:
    <br><br>Collect the data given in <code>LookupResponse</code> and send a new web form using the cardholder browser. 
    <br><br>An example of the post web form is given here. 
</aside>

The user will go through the authentication process and will be redirected to the url that the consumer sent in `LookupRequest.redirect_uri`.

Once the user visits the merchant Url (`LookupRequest.redirect_uri`), The authentication process can be treated as finished. 
The merchant must use `GetTransactionStatus` to get information about the 3DS authentication process.

# TransactionStatus

The *TransactionStatus* object gives information about the results of 3DS authentication in a transaction

You must use the _GetTransactionStatus_ right after the user completed the authentication process and returned to the merchant page.

## TransactionStatus Object

> Example of TransactionStatus Object:

```json
{
    "authentication_status": "Y",
    "validated_signature": true,
    "eci": "05",
    "xid": "3030303030303030303030303030303030303032",
    "cavv": "0000020777351815352670947035181300000000"
}
```


### Arguments

Parameter | Type  | Description  
--------- | ------- | ----------- | ----------
authentication_status | string  | Authentication Status on 3DS
validated_signature | boolean | Has the signature of the messages been validated?
eci | integer | Given ECI flag for the transaction.
xid | number | Transaction identifier resulting from the authentication process.
cavv | number  | Cardholder Authentication Verification Value (CAVV)

- `authentication_status`  Authentication Status on 3DS one of `Y|A|N|U`.

    - `Y` Successful Authentication.
    
    - `A` Successful Attempt.

    - `N` Authentication is not available.

    - `U` Unable to know if authentication is available.
    
- `validated_signature` When communicating with the ACS, message signatures must be validated, this parameter indicates the result of that validation..

- `eci` The *Electronic Commerce Indicator* indicates the level of security and the result of the 3D Secure authorization. See [Eci Codes](#eci-codes)

## Get Transaction Status

> Example Request:

```shell
curl "https://3ds-mpi.placetopay.com/api/transactions/1039" \ 
    -X GET \
    -H "accept: application/json" \ 
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer yourApiKey" \ 
```

> Example Response (200):

```json
{
    "authentication_status": "Y",
    "validated_signature": true,
    "eci": "05",
    "xid": "3030303030303030303030303030303030303032",
    "cavv": "0000020777351815352670947035181300000000"
}
```

Get the status of a transaction in 3DS.

`GET http://example.com/api/transactions/{id}`

### Parameters

Parameter | Type  | Description  
--------- | ------- | ----------- | ----------
id | number  | Transaction Id, given in `LookupResponse`

## Handle TransactionStatus

Verify the `authentication_status` parameter in `TransactionStatus` and proceed as:


Authentication Status | Signature  | Description 
--------- | ------- | ----------- | ----------
Y | true  | Authentication Successful
A | true  | Merchant attempted to authenticate the cardholder.
N | true  | Failed Authentication
U | true  | Could not be authenticated
Y,A,N,U | false  | Untrusted results
