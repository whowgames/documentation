
# Kraken API

## Authentication

This API is designed for server to server communication. Never put the secure key
into your mobile app or web project. 

For the authentication you need a username and secure key. The username and a for
each request generated signature have to be sent in the request header.

```
X-AUTH-USER: demo-user
X-AUTH-SIG: c1a8f9c13b3f917....
```

The signature will be generate in two steps. In the first step you create a string 
with a SHA-512 hashing algorithm. For the hash input you append the request HTTP
Method, the API action and the request Payload. 

```
string = HASH("POST" + "/v1/user/createUsers" + "[{'user_id':'123',...},...]")
```

The second step you create the signature with a HMAC-SHA512 function. The input 
are the string to sign appended with the secure key.

```
signature = HMAC(string + secureKey)
```

## General Information

In the production system running on under the url

https://kraken.whow.net

The sandbox system running on under the url

https://kraken-staging.whow.net

Example of a API request. 

```
POST https://kraken.whow.net/v1/user/createUsers

X-AUTH-USER: demo-user
X-AUTH-SIG: c1a8f9c13b3f917....
Accept: application/json
Content-Type: application/json; charset=utf-8

[{'user_id':'123',...},...]
```

If your request was successful the API send a 200 response with the following 
JSON data. 

```
{
  'status': true,
  'data': [...],
  'errors': null,
}
```

If your request was failed the API send a error response with this JSON data.

```
{
  'status': true,
  'data': null,
  'errors': [
    {'message': 'error message'}, 
    ...
  ],
}
```

Add only the data fields for a record which you have data. Keep off the optional 
fields without content to save network bandwidth.

Data types hints: 

- Datetime values have to be convert into UTC times
- Country values are in 2-letter code (ISO 3166-1 alpha-2)
- Currency values are in 3-letter code (ISO 4217)
- Locale values are 5-letter code with lowercase language code and uppercase country code (e.g. "en_GB" or "en_US")
- The `coin_balance` field is mandatory, but can be zero (0)
- The `game_level` field is mandatory, but if you don't have levels in you game set this to 1


## User Methods

### POST /v1/user/createUsers

Request body:

```
[
  {
    'user_id': string mandatory, 
    'status': string mandatory (Options: 'pending', 'active', 'blocked', 'deleted'),
    'usergroup': string mandatory (Options: 'member', 'guest', 'admin', 'tester'),
    'created': datetime mandatory,
    'updated': datetime|null optional,
    'deleted': datetime|null optional,
    'username': string|null optional,
    'country': string|null optional,
    'locale': string|null optional,
    'gender': string|null optional (Options: 'female', 'male'),
    'birthyear': integer|null optional, 
    'loyalty_level': integer|null optional,
    'portal_signup': string|null optional,
    'portal_active': string|null optional,
    'platform_signup': string|null optional (Options: 'web', 'ios', 'android', 'amazon'),
    'platform_active': string|null optional (Options: 'web', 'ios', 'android', 'amazon'),
    'allow_newsletter': integer|null optional (Options: 0, 1),
    'allow_sms': integer|null optional (Options: 0, 1),
    'social_type': string|null optional (Options: 'facebook', 'vkontakte', 'rambler', 'naszaklasa', 'minijuegos'),
    'social_id': string|null optional,
    'utm_source': string|null optional,
    'utm_medium': string|null optional,
    'utm_term': string|null optional,
    'utm_content': string|null optional,
    'utm_campaign': string|null optional,
    'utm_pub': string|null optional,
    'source_links': array|null optional [
        {
            'type': string mandatory (Options: 'affiliate', 'cobrand'),
            'name': string mandatory,
        }, 
        ...
    ],
  },
  ...
]
```

Success 200 response: 

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

### POST /v1/user/updateUsers

Request body:

```
[
  {
    'user_id': string mandatory, 
    'updated': datetime mandatory,
    'deleted': datetime|null optional,
    'status': string|null optional (Options: 'pending', 'active', 'blocked', 'deleted'),
    'usergroup': string|null optional (Options: 'member', 'guest', 'admin', 'tester'),
    'username': string|null optional,
    'country': string|null optional,
    'locale': string|null optional,
    'gender': string|null optional (Options: 'female', 'male'),
    'birthyear': integer|null optional, 
    'loyalty_level': integer|null optional,
    'portal_active': string|null optional,
    'platform_active': string|null optional (Options: 'web', 'ios', 'android', 'amazon'),
    'allow_newsletter': integer|null optional (Options: 0, 1),
    'allow_sms': integer|null optional (Options: 0, 1),
    'social_type': string|null optional (Options: 'facebook', 'vkontakte', 'rambler', 'naszaklasa', 'minijuegos'),
    'social_id': string|null optional,
    'utm_source': string|null optional,
    'utm_medium': string|null optional,
    'utm_term': string|null optional,
    'utm_content': string|null optional,
    'utm_campaign': string|null optional,
    'utm_pub': string|null optional,
    'source_links': array|null optional
  },
  ...
]
```

Success 200 response: 

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

### POST /v1/user/trackEvents

Request body:

```
[
  {
    'user_id': string mandatory,
    'event_type': string mandatory (Options: 'login', 'logout', 'levelup'),
    'event_time': datetime mandatory,
    'platform': string mandatory (Options: 'web', 'ios', 'android', 'amazon'),
    'coin_balance': integer mandatory,
    'game_level': integer mandatory,
    'portal': string|null optional,
    'session_id': string|null optional,
  },
  ...
]
``` 

Success 200 response: 

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

## Payment Methods

### POST /v1/payment/createPayments

The `vat` field is mandatory, but can be zero (0.00).

The sum of the fields `revenue_net`, `vat` and `fee_payment` must be equal to the `revenue_gross` field.

For the status `pending` and `failed` the fields `paid`, `cancelled`, `repaid` must be `null`.

For the status `paid` the field `paid` must be filled with the time the payment was completed.

For the status `cancelled` and `refunded` the field `paid` must be filled with the time the payment was completed
and the field `cancelled` must be filled with the time the payment was book back.

For the status `repaid` the field `paid` must be filled with the time the payment was completed
and the field `cancelled` must be filled with the time the payment was book back
and the field `repaid` must be filled with the time payment was repaid.

For mobile/app payments use `iTunes`, `GooglePlay` or `Amazon` as payment provider 
and `in-app` as payment method. 

If you use the Whow payment service use the field `paymentMethod` and `paymentProvider` 
from the `billing` notification as payment provider and method.

Request body:

```
[
  {
    'payment_id': string mandatory,
    'user_id': string mandatory,
    'status': string mandatory (Options: 'pending', 'paid', 'failed', 'cancelled', 'refunded', 'repaid'),
    'country': string mandatory,
    'platform': string mandatory (Options: 'web', 'ios', 'android', 'amazon'),
    'payment_method': string mandatory,
    'payment_provider': string mandatory,
    'currency': string mandatory,
    'revenue_gross': float mandatory,
    'revenue_net': float mandatory,
    'vat': float mandatory,
    'fee_payment': float mandatory,
    'fee_chargeback': float|null optional,
    'goods': array mandatory[
      {
        'type': string mandatory,
        'amount': integer mandatory,
        'data': string|null optional,
      },
      ...
    ],
    'created': datetime mandatory,
    'paid': datetime|null optional,
    'cancelled': datetime|null optional,
    'repaid': datetime|null optional,
    'bonus': float|null optional,
    'source': string|null optional (Options: 'offer', 'catalog'),
  },
  ...
]
```

Success 200 response: 

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

### POST /v1/payment/updatePayments

For status and their required time fields see [createPayments function](#post-v1paymentcreatepayments)

If you have a `fee_chargeback` value, is mandatory to set `currency`. 

Request body:

```
[
  {
    'payment_id': string mandatory,
    'status': string mandatory (Options: 'pending', 'paid', 'failed', 'cancelled', 'refunded', 'repaid'),
    'user_id': string|null mandatory,
    'currency': string|null optional, 
    'fee_chargeback': float|null optional,
    'paid': datetime|null optional,
    'cancelled': datetime|null optional,
    'repaid': datetime|null optional,
  },
  ...
]
```

Success 200 response: 

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

## Game Methods

### POST /v1/game/play

Use this method for any action of a user in the game. For example the user play 
a round or spin on a slot. For all actions the user bet or win coins or real money. 

The `play_started` and `play_ended` fields can be equal. 

If you add a `bet_amount` value, you have also add `bet_currency` and `bet_type`.

If you add a `win_amount` value, you have also add `win_currency` and `win_type`.

Request body:

```
[
  {
   'user_id': string mandatory,
   'game_id': string mandatory, 
   'play_started': datetime mandatory,
   'play_ended': datetime mandatory,
   'play_result': string mandatory (Options: 'win', 'lost', 'bet', 'exchange', 'initial'),
   'platform': string mandatory (Options: 'web', 'ios', 'android', 'amazon'),
   'coin_balance': integer mandatory,
   'game_level': integer mandatory,
   'bet_amount': float|null optional,
   'bet_currency': string|null optional,
   'bet_type': string|null optional (Options: 'virtual', 'real'),
   'win_amount': float|null optional,
   'win_currency': string|null optional,
   'win_type': string|null optional (Options: 'virtual', 'real'),
   'portal': string|null optional,
   'session_id': string|null optional,
  },
  ...
]
```

Success 200 response:

```
{
  'status': true,
  'data': {
    'success': integer,
    'error': integer,
  },
  'errors': null,
}
```

## Payout Methods

### POST /v1/payout/book

Request body:

```
[
  {
   'user_id': string mandatory,
   'paid_out': datetime mandatory,
   'type': string mandatory (Options: 'money'),
   'amount': float mandatory,
   'currency': string mandatory,
   'country': string mandatory,
   'platform': string mandatory (Options: 'web', 'ios', 'android', 'amazon'),
   'transaction_id': string|null optional,
   'portal': string|null optional,
   'session_id': string|null optional,
  },
  ...
]
```
