**Whow API Integration Guide**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Introduction](#introduction)
- [Getting started](#getting-started)
- [Additional Parameters](#additional-parameters)
- [Structure of URIs](#structure-of-uris)
- [Authentication](#authentication)
    - [Create a canonical request](#create-a-canonical-request)
    - [Create a String to Sign](#create-a-string-to-sign)
    - [Calculate the Signature](#calculate-the-signature)
    - [Add the Signing Information to the Request](#add-the-signing-information-to-the-request)

- [Best Practises](#best-practises)
    - [When to use which call](#when-to-use-which-call)
    - [Periodic Wallet Update in Idle Mode](#periodic-wallet-update-in-idle-mode)

- [Required features](#required-features)
    - [Wallet/Chip sizes](#walletchip-sizes)
    - [Custom bet sizes](#custom-bet-sizes)
    - [Pre-selected bet amount](#pre-selected-bet-amount)

- [Calls](#calls)
    - [GameSessions](#gamesessions)
        - [GameSessions::get](#gamesessionsget)
        - [GameSessions::wallet](#gamesessionswallet)
        - [GameSessions::bet](#gamesessionsbet)
        - [GameSessions::close](#gamesessionsclose)
        - [GameSessions::cancel](#gamesessionscancel)
        - [GameSessions::validateFreespins](#gamesessionsvalidatefreespins)
        - [GameSessions::play](#gamesessionsplay)
    - [GameEvents](#gameevents)
        - [GameEvents::trigger](#gameeventstrigger)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  |
|-------------|------------|----------------------------------------------------|-----------|
| 0.1.0       | 28.08.2014 | Initial version                                    | fschemmer |
| 1.0.0       | 05.09.2014 | Camel Casing                                       | fschemmer |
| 1.0.1       | 08.09.2014 | Added user variables                               | fschemmer |
| 1.0.2       | 13.10.2014 | Added new bet amounts, virtualAmount and stop on autoPlay | fschemmer |
| 1.1         | 16.02.2015 | Update document from .docX -> .md, various updates & fixes, added "Best Practices" section | fschemmer |
| 1.2         | 23.05.2016 | Updated copyright, added freespins with fixed bet amount | fschemmer |
| 1.3         | 16.06.2016 | Added GameEvents namespace and removed REST support | fschemmer |
| 1.3.1       | 14.11.2016 | Added birthday to GameSessions::get | fschemmer |
| 1.3.2       | 08.08.2017 | Removed deprecated steps object | mkercmar |
| 1.3.3       | 29.09.2017 | Added documentation about additional parameters | sambros |
| 1.3.4       | 27.05.2018 | Name, birthday and gender are no longer filled with real values, instead dummy data is transmited | mkercmar |
| 1.3.5       | 30.11.2018 | Remove gameData object from documentation & payload examples | mkercmar |
| 1.3.6       | 13.03.2019 | All mentions of level, levelProgress and levelUp values removed as this is depracted | mkercmar |
| 1.3.7       | 22.05.2019 | Added documentation about defaultBet | sambros |
| 1.3.7.1     | 19.11.2019 | Remove info about animation | mkercmar |
| 1.3.7.2     | 27.01.2020 | Changed timestmp to integer | mkercmar |
| 1.4         | 09.06.2020 | Added required features section | mkercmar |

Copyright
=========

Copyright © 2014 - 2020 Whow Games GmbH. All rights reserved.

Introduction
============

The Whow API serves as an API to connect your games and services to the Whow backend.
This document is intended for software developers and other technical positions.

Getting started
===============

The Whow API is based on open standards, such as JSON, so you can use any language of choice to access the Whow API. So far we do not have any official framework that helps you connect your application to our API.

It is crucial that all of your requests to the Whow API are sent from a server. If you send requests from a client your key pair can be hijacked and used to manipulate critical user data. More on this topic can be found within the Authentication chapter in this documentation.

Additional Parameters
=================

We're adding additional parameters to your launch url. Please use them to set the initital game settings.

> Notice: we're taking care about reloading your game, when the user changes on of those inside our settings panel


| **Name** | **Possible Values** | **Description**                    |
|----------|----------|--------------------------------------|
| sound     | 0\|1   | Enable the Sound 0 = sound off, 1 = sound on   |

Structure of URIs
=================

Your application will access the Whow API via HTTP(s) requests. All requests made to the Whow API and from the Whow API must be JSON encoded and made via POST.

URIs will have the following structure:

><https://api.jackpot.de/namespace/action/method>

The exact URIs for each call can be found within this documentation.

Authentication
==============

In order to access the Whow API you need to authenticate yourself for each request. This is done by providing your public API access key, a signature, used headers to generate the signature and the used hash mechanism to generate the signature within an additional HTTP header.

>Authorization: SHA256 Credential=ef9f237e8aaec2893b6b2121, SignedHeaders=content-type;host;x-whow-date, Signature=0a858bcd112af12ef1f195e3b7ba3c8e30352dd5c02e09be8ca1006ca228c576

The next steps will guide you through the process of creating the authorization header. To give you an idea of a sample request without any authorization header consider the following example of how an user update request might look like.

>PUT [https://api.jackpot.de/users/ef9f237e8aaec2893b6b2121 HTTP/1.1](https://api.jackpot.de/users/ef9f237e8aaec2893b6b2121%20HTTP/1.1)

>Host: api.jackpot.de
Content-Type: application/json; charset=utf-8
X-whow-date: 20140828T163000Z

>{"name":"John Doe"}

After you have created a signature for this call it might look like this.

>PUT [https://api.jackpot.de/users/ef9f237e8aaec2893b6b2121 HTTP/1.1](https://api.jackpot.de/users/ef9f237e8aaec2893b6b2121%20HTTP/1.1)

>Authorization: SHA256 Credential=ef9f237e8aaec2893b6b2121, SignedHeaders=content-type;host;x-whow-date, Signature=7921c3698fb7f40ed9cea807c1dc135e2739c4d5c56bd6a7e090bec4eee7f5c4
Host: api.jackpot.de
Content-Type: application/json; charset=utf-8
X-whow-date: 20140828T163000Z

>{"name":"John Doe"}

Create a canonical request
--------------------------

To get started with the signing process you need to create a string that includes all necessary request parameters in a canonical (standardized) format. You need to follow these steps exactly otherwise your request will be invalid because the calculated signature on the server side will not match your provided signature.

Pseudocode of a canonical request string:

>CanonicalRequest =
HTTPRequestMethod + ‘\\n’ +
CanonicalURI + ‘\\n’ +
CanonicalHeaders + ‘\\n’ +
SignedHeaders + ‘\\n’ +

>RequestString = Lower(Hash(Payload))

The function *Hash* in the example request above represents the hashing algorithm you want to use, typically this would be SHA-256. Lower is a function which returns the given input string as lowercase string.

To create a canonical request, concatenate the following components from each step into a single string:

1.  Start with the HTTP request method followed by a newline character.
    >POST\\n

2.  Add the canonical URI parameter followed by a newline character. The canonical URI is everything from the HTTP host to the question mark.
    >/users/ef9f237e8aaec2893b6b2121\\n

3.  Add the canonical headers followed by a newline character. The canonical headers consist of a list of HTTP headers that are included in the request to the Whow API. You can include whatever headers you want in here as long as they are sent as real headers in the request as well. Make sure that all headers are sorted alphabetically. All header names (the part in front of the colon) must be lowercase and without leading or trailing whitespaces. The value for each header must be without leading and trailing whitespaces as well. After each header line there is a newline character.
    >content-type:application/json; charset=utf-8\\n
    host:api.jackpot.de\\n
    x-whow-date:20140828t163000z\\n
    \\n

4.  Add the signed headers followed by a newline character. This value is a list of the names of the headers that were included as canonical headers in step 3. Each header that is specified here must be in the real request and in the canonical headers as well. Make sure that each header name is lowercase and that all header names are sorted alphabetically. All header names are then concatenated into a string separating each header with a semi colon.
    >content-type;host;x-whow-date\\n

5.  Use a hash function like SHA-256 to create a hash value of the JSON payload you are sending with your request. Keep in mind that by HTTP standards there can’t be a payload if you are using GET as request method. In this case you need to provide an empty string *""* into the hashing method. In case of any other request method without payload you need to supply an empty JSON array ‘*[]*’ as string. In case you have a JSON string as payload you will use this string as is.
    >Sample payload:
    >
    >     {"name":"John Doe"}
    >Hashed payload:
    >
    >     f9302c25f1b5941903d562d3fc43dfb0e814c65bf6f63ba5baa0a758d34982d3

6.  To construct the finished canonical request combine all the components from each steps as a single string. As already mentioned each component must end with a newline character. The resulting canonical request should look like this.
    >POST\\n
    /users/ef9f237e8aaec2893b6b2121\\n
    content-type:application/json; charset=utf-8\\n
    host:api.jackpot.de\\n
    x-whow-date:20140828t163000z\\n
    \\n
    content-type;host;x-whow-date\\n
    f9302c25f1b5941903d562d3fc43dfb0e814c65bf6f63ba5baa0a758d34982d3

7.  Create a hash of the canonical request by using the same algorithm you already used to hash the payload. The hashed canonical request must be represented as a string of lowercase hexadecimal characters.
    The resulting hashed canonical request should look like this. You will use this value again in later stages of the signing process.
    >8ea0ab5b0cb5f65af16bc01340e2129ba426b350852237c54a3f66821b1166cf

Create a String to Sign
-----------------------

The string to sign includes more information about your request and the canonical request you created in the step before. This string will be used when calculating the actual signature in the end.

Pseudocode of a *string to sign* string:
> StringToSign =
>     Algorithm + ‘\\n’ +
>     RequestDate + ‘\\n’ +
>     HashedCanonicalRequest

1.  Start with the algorithm you already used for signing your canonical request. It is crucial that you will use the same algorithm for everything you do.
    >SHA256\\n

2.  Append the request date followed by a newline character. The date must be in ISO8601 format and must match every previous date you used
    >20140828T163000Z\\n

3.  Append the hash of the canonical request that you created in the step before.
    >8ea0ab5b0cb5f65af16bc01340e2129ba426b350852237c54a3f66821b1166cf

The end result of this process should look like this then:
> SHA256\\n
> 20140828T163000Z\\n
> 8ea0ab5b0cb5f65af16bc01340e2129ba426b350852237c54a3f66821b1166cf

Calculate the Signature
-----------------------

Before you calculate the signature you need to derive a signing key from your secret key which is provided with the public API access key by Whow when you asked for access to the Whow API. If you have no API access key and secret key yet, please contact us for further assistance.

1.  Derive your signing key by using your secret key to create a series of hash-based message authentication codes (HMACs). In the following example HMAC represents an HMAC-SHA256 function. However: You need to use the same hashing algorithm within the HMAC function as you used for all the previous hashing operations as well. The result of each hash functions becomes input for the next one. The following pseudo code will use the HMAC function with (key, data) as parameters:**
    >Secret = Your Secret Key
    Date = HMAC("whow" + Secret, Date)
    SigningKey = HMAC(Date, "whow\_request")

    Note that the variable *Date* used in the hashing process is just the date (for example, 20140828), not a complete date and time.

2.  Calculate the signature. To do this, use the signing key that you received when doing the previous step as key and the *string to sign* as data from the previous chapter. The following pseudo code will use the HMAC function with (key, data) as parameters:
    >Signature = HMAC(SigningKey, StringToSign)

Add the Signing Information to the Request
------------------------------------------

Now that you have calculated your request signature you can add it as a header to your request. The header needs to look like this (placeholder):

>Authorization: Algorithm Credential=API access key, SignedHeaders=SignedHeaders, Signature=Signature

With the values from the examples it should look like this:

>Authorization: SHA256 Credential=ef9f237e8aaec2893b6b2121, SignedHeaders=content-type;host;x-whow-date, Signature=0a858bcd112af12ef1f195e3b7ba3c8e30352dd5c02e09be8ca1006ca228c576

Note that there is no comma behind the algorithm parameter, but a space. The key value pairs afterwards are comma separated. With this header added to your request you should be able to access the API without any problems.

Best Practises
==============

Before you get an overview over all calls it would be good to give you some tips about what is best practise with our API and what you should try to avoid.

When to use which call
----------------------

It’s important for the users feeling, that you’re going to fire the calls when the user finaly sees the numbers. Otherwise he will be notified with the new chips, before he sees what he got in your game.

Best practice for a spin would be:

1. Call *GameSessions::bet*, this method will deduct the chips from the wallet balance

2. Start spinning the wheels, animation should end before doing other calls

3. (Optional) If a user won and have a bonus game mechanic like a win ladder user should have the option to use it

4. Call *GameSessions::close*, **with** and also **without** a winning, so we know the spin ended

Periodic Wallet Update in Idle Mode
-----------------------------------

Since there are a lot of other actions running on our casino which can cause the users wallet ingame to get out of sync with the one from the casino you should periodically fetch the wallet and update the chips.
To avoid spamming the API it would be good to only query this information when the game is in some kind of idle mode. This means for example that the user has not done any action within the last 30 seconds. After that time frame it is best to call the API every 30 seconds to fetch a new balance. This can be achieved by using the [GameSessions::wallet](#gamesessionswallet) call.

Required features
=================

Wallet/Chip sizes
-----------------

Our total bet sizes have a range of 100 chips up to 100 million chips per spin.

Depending on the win ratios during a spin you need to be able to handle chip values up to billions.

On the wallet size of a user it can therefore go up to trillions chips.

Custom bet sizes
----------------

As we are an social casino we are using individual bet sizes per user. They are increasing with the level.

On higher levels the lower bet sizes are slowly getting disabled, as they will not have any good experience anymore and higher values are added.

Bets will be initially transmitted on **GameSessions::get** call.

Values can be found in Response: *payload -> game -> settings -> bets*. It is an simple JSON array.

We normally transmit bet per line, for a 20 liner slot it would be f.e.:

```
"bets":[20,30,50,100,200,400,800,1200,1600,2000,2500]
```

Or we could optionally configure to send total bets, so it would be f.e.:

```
"bets":[400,600,1000,2000,4000,8000,16000,24000,32000,40000,50000]
```

Also every sequent call is returning the bets array as it might increase during a game sessions. Ideally you should be able to change the available bet sizes during a game sessions. (*But: user should be able to continue to play his current bet sizes until he manually adjustes it, do not automatically change the bet amount*)

Pre-selected bet amount
-----------------------

Our portal has a couple of features where a user can pre-select the inital bet amount which the slot should initially started with.

It is ransmitted on **GameSessions::get** call.

Value can be found in Response: *payload -> game -> settings -> defaultBet*.

Depending on *Custom bet sizes* it is either bet per line or total bet amount.

If no value is transmitted you should use the last played bet size.

Calls
=====

Each request you make to the Whow API will have the same basic JSON response format. There will always be a *status* field with the status of your request as a standardized HTTP status code. In case the *status* is not 200 there will be an *errors* array within the response as well. In every case there will be a *payload* object which will include all the other objects which you will receive as response from your call.

This is a success JSON encoded response to give you a better understanding:

>{
   **"status"**:200,
   **"payload"**:{
      **"user"**:{
         "**id"**:"53fc9eb31b4d5eef118b4569",
         **"locale"**:"de\_DE",
         **"wallet"**:{  
            **"chips"**:1217900
         }
      },
      **"game"**:{  
         **"settings"**:{  
            **"bets"**:[  
               50,
               100,
               250,
               500,
               1000,
               2500,
               5000,
               10000,
               25000
            ]
         }
      }
   }
}

This is an error, JSON encoded, response:

>{
   **"status"**:400,
   **"errors"**:[
      "round is not open anymore"
   ],
   **"payload"**:[
   ]
}

Resulting from this you want to check the payload after each and every request and update the corresponding data within your application. Keep in mind that normally you just receive the information you requested. So if you are requesting the current balance of a user wallet you will not receive the whole user object with all the data within the payload but rather a user object which just contains the wallet object:

>{
   **"status"**:200,
   **"payload"**:{
      **"user"**:{
         **"wallet"**:{
            **"chips"**:1217900
         }
      }
   }
}

GameSessions
------------

GameSessions::get
-----------------

The call *get* is used to request data for the current game session, including game and user data. This call can only be used once and is not allowed a second time for the given token. Make sure to **always** call this method on game startup.

Please note that game settings can be changed by subsequent requests and events like leveling up for example and are therefor not fix.

In case you are using the freespin object, which is part of the game object, to obtain freespins from the casino, make sure to validate the freespins with the extra call [GameSessions::validateFreespins](#gamesessionsvalidatefreespins) after you have received them in this call.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/get/{:token}>

>The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>No payload needed so please provide an empty JSON array here.

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/get/53fdadc3499a9f85368b4567>
>
>Payload
>
>     []

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                    | **Description** |
|----------|----------|--------------------------------------|-----------------|
| user     | Object   | {"\_id":"53fc9eb31b4d5eef118b4569","username":"john\_doe"}   | user object     |
| game     | Object   | {"settings":{"bets":[50, 100, 250]}} | game object     |

The user object contains the following parameters:

| **Name**      | **Type**   | **Example Value**          | **Description**                                           |
|---------------|------------|----------------------------|-----------------------------------------------------------|
| id            | String     | "53fc9eb31b4d5eef118b4569" | id of the user                                            |
| locale        | Char(5)    | "de\_DE"                   | locale of the user                                        |
| wallet        | Object     | {"chips": 1250.00}         | wallet object                                             |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

The game object contains the following parameters:

| **Name** | **Type** | **Example Value**        | **Description**      |
|----------|----------|--------------------------|----------------------|
| settings | Object   | {"bets": [50, 100, 250]} | game settings object |
| freespins | Object Array | [{"id": "53f...", "amount": 12, "betAmount": 1250.00}] | freespins object |

The game settings object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| bets     | Array    | [400, 8000, 125000]    | currently allowed bet sizes |
| defaultBet     | Integer    | 40000    | default bet which should be selected on start (even if it does not exist in bets). Can be "null", then please use the last played bet size |

The freespins object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| id       | String   | "53fc9eb31b4d5eef118b4569" | id of the freespin set |
| amount   | Integer  | 12 | amount of freespins |
| betAmount | Float(19,4) | 1250.00 | bet amount of each freespin |

####

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 400             | token seems to be corrupt please request a new one  |
| 410             | token has already been used to retrieve this game session |

GameSessions::wallet
--------------------

The call *wallet* is used to request the current user wallet with all available balances.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/wallet/{:token}>

>The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>No payload needed so please provide an empty JSON array here.

####

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/wallet/53fdadc3499a9f85368b4567>
>
>Payload
>
>     []

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**             | **Description** |
|----------|----------|-------------------------------|-----------------|
| user     | Object   | {"wallet": {"chips":1250.00}} | user object     |

The user object contains the following parameters:

| **Name** | **Type** | **Example Value**  | **Description** |
|----------|----------|--------------------|-----------------|
| wallet   | Object   | {"chips": 1250.00} | wallet object   |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 400             | token seems to be corrupt please request a new one |

GameSessions::bet
-----------------

The call *bet* is used to bet on a game round. Important: If there is no round id specified within the payload of this call, a new game round will be started.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/bet/{:token}>

>The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name**      | **Type** | **Example Value**   | **Description**                                                                                                          |
|---------------|----------|---------------------|--------------------------------------------------------------------------------------------------------------------------|
| betAmount     | Float    | 1250                | amount to bet in this round                                                                                              |
| virtualAmount | Float    | 0                  | amount to bet in case of free spins; important: this parameter is optional and only used in special cases for freespins!         |
| roundId       | String   | "ca5217a205e148ba…" | round id to bet on; important: this parameter is optional! if it is not provided the backend will start a new game round |

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/bet/53fdadc3499a9f85368b4567>
>
>Payload
>
>     {"betAmount":1250,"roundId":"ca5217a205e148ba85f73bc37ad7e0a0"}

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                     | **Description**   |
|----------|----------|---------------------------------------|-------------------|
| user     | Object   | {"wallet": {"chips":1250.00}}         | user object       |
| round    | Object   | {"id":[{…}]…}                         | game round object |
| game     | Object   | {"settings":{"bets": [50, 100, 250]}} | game object       |

The user object can contain the following parameters but doesn’t need to have them:

| **Name**      | **Type** | **Example Value**       | **Description**                                                       |
|---------------|----------|-------------------------|-----------------------------------------------------------------------|
| wallet        | Object   | {"chips": 1250.00}      | wallet object                                                         |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

The game round object contains the following parameters:

| **Name** | **Type**     | **Example Value**      | **Description**                                                              |
|----------|--------------|------------------------|------------------------------------------------------------------------------|
| id       | String       | 20a7bafff1b24f22b33d8f128a00e54d | round id |
| betAmount | Integer     | 6250                   | total bet amount of round |
| winAmount | Integer     | 0                      | total win amount of round |
| timestamp | Integer      | 1502200341 | timestamp of last change |

The game object can contain the following parameters but doesn’t need to have them. Most likely game settings are changed on a user level up for example:

| **Name**     | **Type** | **Example Value**        | **Description**                                   |
|--------------|----------|--------------------------|---------------------------------------------------|
| settings     | Object   | {"bets": [50, 100, 250]} | settings object                                   |

The game settings object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| bets     | Array    | [50, 100, 250]    | currently allowed bet sizes |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 110             | error while trying to book chips from/to the user, most likely the user has not enough chips |
| 400             | token seems to be corrupt please request a new one |
| 400             | parameters are missing                              |
| 400             | round status is not open                            |
| 400             | round id is not valid                               |

GameSessions::close
-------------------

The call *close* is used to close a game round and determine the winnings of a user.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/close/{:token}>

The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name**  | **Type** | **Example Value**   | **Description**                                |
|-----------|----------|---------------------|------------------------------------------------|
| winAmount | Float    | 2500                | closing win amount for this round              |
| roundId   | String   | "ca5217a205e148ba…" | round id to close                              |

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/close/53fdadc3499a9f85368b4567>
>
>Payload
>
>     {"winAmount":2500,"roundId":"ca5217a205e148ba85f73bc37ad7e0a0"}

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                     | **Description**   |
|----------|----------|---------------------------------------|-------------------|
| user     | Object   | {"wallet": {"chips":1250.00}}         | user object       |
| round    | Object   | {"id":[{…}]…}                         | game round object |
| game     | Object   | {"settings":{"bets": [50, 100, 250]}} | game object       |

The user object can contain the following parameters but doesn’t need to have them:

| **Name** | **Type** | **Example Value**  | **Description** |
|----------|----------|--------------------|-----------------|
| wallet   | Object   | {"chips": 1250.00} | wallet object   |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

The game round object contains the following parameters:

| **Name** | **Type**     | **Example Value**      | **Description**                                                              |
|----------|--------------|------------------------|------------------------------------------------------------------------------|
| id       | String       | 20a7bafff1b24f22b33d8f128a00e54d | round id |
| betAmount | Integer     | 6250                   | total bet amount of round |
| winAmount | Integer     | 2000                   | total win amount of round |
| timestamp | Integer      | 1502200341 | timestamp of last change |

The game object can contain the following parameters but doesn’t need to have them. Most likely game settings are changed on a user level up for example:

| **Name**     | **Type** | **Example Value**        | **Description**                                   |
|--------------|----------|--------------------------|---------------------------------------------------|
| settings     | Object   | {"bets": [50, 100, 250]} | settings object                                   |

The game settings object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| bets     | Array    | [50, 100, 250]    | currently allowed bet sizes |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 110             | error while trying to book chips from/to the user   |
| 400             | token seems to be corrupt please request a new one |
| 400             | parameters are missing                              |
| 400             | round status is not open                            |
| 400             | round id it not valid                               |

GameSessions::cancel
--------------------

The call *cancel* is used to cancel a game round and return the bets to the user.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/cancel/{:token}>

The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name** | **Type** | **Example Value**   | **Description**                                |
|----------|----------|---------------------|------------------------------------------------|
| roundId  | String   | "ca5217a205e148ba…" | round id to close                              |

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/cancel/53fdadc3499a9f85368b4567>
>
>Payload
>
>     {"roundId":"ca5217a205e148ba85f73bc37ad7e0a0"}

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                     | **Description**   |
|----------|----------|---------------------------------------|-------------------|
| user     | Object   | {"wallet": {"chips":1250.00}}         | user object       |
| round    | Object   | {"id":[{…}]…}                         | game round object |
| game     | Object   | {"settings":{"bets": [50, 100, 250]}} | game object       |

The user object can contain the following parameters but doesn’t need to have them:

| **Name** | **Type** | **Example Value**  | **Description** |
|----------|----------|--------------------|-----------------|
| wallet   | Object   | {"chips": 1250.00} | wallet object   |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

The game round object contains the following parameters:

| **Name** | **Type**     | **Example Value**      | **Description**                                                              |
|----------|--------------|------------------------|------------------------------------------------------------------------------|
| id       | String       | 20a7bafff1b24f22b33d8f128a00e54d | round id |
| betAmount | Integer     | 6250                   | total bet amount of round |
| winAmount | Integer     | 2000                   | total win amount of round |
| timestamp | Integer      | 1502200341 | timestamp of last change |

The game object can contain the following parameters but doesn’t need to have them. Most likely game settings are changed on a user level up for example:

| **Name**     | **Type** | **Example Value**        | **Description**                                   |
|--------------|----------|--------------------------|---------------------------------------------------|
| settings     | Object   | {"bets": [50, 100, 250]} | settings object                                   |

The game settings object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| bets     | Array    | [50, 100, 250]    | currently allowed bet sizes |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 110             | error while trying to book chips from/to the user   |
| 400             | token seems to be corrupt please request a new one |
| 400             | parameters are missing                              |
| 400             | round status is not open                            |
| 400             | round id it not valid                               |
| 400             | no valid entries for this round to cancel           |

GameSessions::validateFreespins
--------------------

The call *validateFreespins* is used to validate freespins received within the [GameSessions::get](#gamesessionsget) call. The validation is done by providing the ids from the freespin objects.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/validateFreespins/{:token}>

The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name** | **Type** | **Example Value**   | **Description**                                |
|----------|----------|---------------------|------------------------------------------------|
| ids | Array | ["ca5217a205e148ba85f73bc37ad7e0a0"]  | freespin ids to validate |

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/validateFreespins/53fdadc3499a9f85368b4567>
>
>Payload
>
>     {"ids":["ca5217a205e148ba85f73bc37ad7e0a0"]}

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                     | **Description**   |
|----------|----------|---------------------------------------|-------------------|
| validIds | Array | ["ca5217a205e148ba85f73bc37ad7e0a0"] | list of valid and validated ids |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 400             | token seems to be corrupt please request a new one  |

GameSessions::play
------------------

The call *play* is used to instantly play a complete game round with a given bet and win amount. In case you are playing free spins or some other free rounds where the user does not bet currency of his own please use virtualAmount instead of betAmount. If betAmount + virtualAmount equals zero the call will fail.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game\_sessions/action/play/{:token}>

>The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name**      | **Type** | **Example Value**  | **Description**                                |
|---------------|----------|--------------------|------------------------------------------------|
| betAmount     | Float    | 1250               | amount to bet in this round                    |
| virtualAmount | Float    | 0                  | amount to bet in case of free spins; important: this parameter is optional and only used in special cases for freespins!         |
| winAmount     | Float    | 0                  | amount the user will win in this round         |

####

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_sessions/action/play/53fdadc3499a9f85368b4567>
>
> Payload
>
>     {"betAmount":1250,"winAmount":0}

#### Response on success

The following objects will be within the payload object in the JSON response:

| **Name** | **Type** | **Example Value**                     | **Description**   |
|----------|----------|---------------------------------------|-------------------|
| user     | Object   | {"wallet": {"chips":1250.00}}         | user object       |
| round    | Object   | {"id":[{…}]…}                         | game round object |
| game     | Object   | {"settings":{"bets": [50, 100, 250]}} | game object       |

The user object can contain the following parameters but doesn’t need to have them:

| **Name**      | **Type** | **Example Value**     | **Description**                                                       |
|---------------|----------|-----------------------|-----------------------------------------------------------------------|
| wallet        | Object   | {"chips": 1250.00}    | wallet object                                                         |

The wallet object contains the following parameters:

| **Name** | **Type**    | **Example Value** | **Description**                |
|----------|-------------|-------------------|--------------------------------|
| chips    | Float(19,4) | 1250.5000         | chip amount of the user wallet |

The game round object contains the following parameters:

| **Name** | **Type**     | **Example Value**      | **Description**                                                              |
|----------|--------------|------------------------|------------------------------------------------------------------------------|
| id       | String       | 20a7bafff1b24f22b33d8f128a00e54d | round id |
| betAmount | Integer     | 6250                   | total bet amount of round |
| winAmount | Integer     | 2000                   | total win amount of round |
| timestamp | Integer      | 1502200341 | timestamp of last change |

The game object can contain the following parameters but doesn’t need to have them. Most likely game settings are changed on a user level up for example:

| **Name**     | **Type** | **Example Value**        | **Description**                                   |
|--------------|----------|--------------------------|---------------------------------------------------|
| settings     | Object   | {"bets": [50, 100, 250]} | settings object                                   |

The game settings object contains the following parameters:

| **Name** | **Type** | **Example Value** | **Description**             |
|----------|----------|-------------------|-----------------------------|
| bets     | Array    | [50, 100, 250]    | currently allowed bet sizes |

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 110             | error while trying to book chips from/to the user, most likely the user has not enough chips |
| 400             | token seems to be corrupt please request a new one |
| 400             | parameters are missing                              |

GameEvents
------

GameEvents::trigger
--------------------

The call *trigger* is used to trigger events based on an event type and additional event data, depending on the event type. Further details regarding events can be found within the [game_events.md](https://github.com/whowgames/documentation/blob/master/API/game_events.md) document, which is located in our document repository on github.com.

Since the events parameter is an array you can publish multiple events at the same time.

It should be seen as kind of fire and forget, at least the transmission should be non-blocking and do not prevent any further user interaction with the slot.

#### HTTP Method

>POST

#### URL

><https://api.jackpot.de/game_events/action/trigger/{:token}>

The *{:token}* parameter is the token assigned to your game session which was handed over to your game on startup.

#### Payload

>The following payload parameters are required:
>
| **Name** | **Type** | **Example Value**   | **Description**                                |
|----------|----------|---------------------|------------------------------------------------|
| events | Object Array | [{"type":"big_win","value":1250000}] | array of event objects - see below |

The event object should contain the following parameters:

| **Name** | **Type** | **Example Value**        | **Description**      |
|----------|----------|--------------------------|----------------------|
| type | String | "win"  | event type taken from the *events.md* document |
| value | String or Float(19,4) or Boolean | 1250000  | value for the given event type |

Additional to these two mandatory parameters each event can contain additional paramters which are described within the *events.md* document. Make sure to include all mandatory parameters for each event.

#### Example request

>URL
>
>     POST <https://api.jackpot.de/game_events/action/trigger/53fdadc3499a9f85368b4567>
>
>Payload
>
>     {"events":[{"type":"big_win","value":1250000}]}

#### Response on success

In case of a successful event transmission the payload within the response will be empty, represented by an empty JSON array, since there is no information the game could use after transmitting events. However: The status will be *200*, indicating that everything went well.

#### Response on failure

| **Status code** | **Description**                                     |
|-----------------|-----------------------------------------------------|
| 400             | token seems to be corrupt please request a new one |
| 401             | unknown event type |
| 402             | missing paramter for given event type |
