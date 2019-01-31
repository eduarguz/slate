---
title: 3DS MPI - API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.placetopay.com/web/'>Sign Up for a Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the 3DS-MPI API! You can use our API to access 3DS-MPI API endpoints, which can get you started with 3DS authentication and other resources in the MPI.

We have language bindings in Shell, You can view examples in the dark area to the right.

# Authentication

3DS-MPI uses API keys to authenticate requests.

3DS-MPI expects for the API key to be included in all API requests to the server in a header that looks like the following:


> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl POST "http:"
  -H "Authorization: Bearer YourApiKeyHere"
```

> Make sure to replace `YourApiKeyHere` with your API key.

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

```shell
curl "https://www.example.com/api/lookup" \ 
    -X POST \
    -H "accept: application/json" \ 
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer feafaefaew" \ 
    -d {"pan":4111111111111111,"expiration_year":2029,"expiration_month":4,"amount":23999.69,"currency":"USD","redirect_uri":"https://www.merchanturl.com","installments":3,"recurrence":true,"recurrence_frequency":20,"recurrence_expiration":"20201122","locale":"en","disable_redirect":false}
```

The _LookUpRequest_ is responsible for initiating user authentication. 

You must use the _LookUpRequest_ right after you have captured the payment information, which must contain the final amount of the transaction and the credit card information.

The response message returned by the MPI contains information about the enrollment of the card in 3DS.

## LookupRequest Object

> Example of LookupRequest Object:

```json
{
    "pan":4111111111111111,
    "expiration_year":2029,
    "expiration_month":4,
    "amount":23999.69,
    "currency":"USD",
    "redirect_uri":"https://www.merchanturl.com",
    "installments":3,
    "recurrence":true,
    "recurrence_frequency":20,
    "recurrence_expiration":"20201122",
    "locale":"en",
    "disable_redirect":false,
}
```

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

- `redirect_uri` Consumer Url, the user will return to this url once the authentication process is finished.
- `installments` If applicable, the amount of installments selected for payment
- `recurrence` If applicable, is recurrence enabled for the payment?
- `recurrence_frequency` required if recurrence is true
- `recurrence_expiration` required if recurrence is true, format must be YYYYMMDD
- `locale` Localization language to be used in messages, choose from `es|en`
- `disable_redirect` if `true`,  the MPI will not handle the automatic redirection to the ACS. The API will return the necessary data for the consumer to make the form post 

### HTTP Request

`POST http://example.com/api/lookup`

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

