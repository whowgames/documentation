**Publisher events**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [Events](#events)
  - [Example Message:](#example-message)
  - [Type: gameLoaded](#type-gameloaded)
      - [Parameters](#parameters)
  - [Type: gameError](#type-gameerror)
      - [Parameters](#parameters-1)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  |
|-------------|------------|----------------------------------------------------|-----------|
| 0.1.1       | 05.04.2021 | Added game error call                                    | sambros |
| 0.1.0       | 01.03.2021 | Initial version                                    | sambros |

Copyright
=========

Copyright © 2022 Whow Games GmbH. All rights reserved.

Introduction
============

This document serves as an explanation of different publisher events published from the game via Java Script's postMessage function.

Events
======

Events are used to track/react on different events given from the game provider.

All events **must have** the paramter *type*, *payload* and *token*.

TODO: check mobile

Example Message:
---------

```json
{
    "type": "gameLoaded",
    "token": "95434fcf2653091660c7320",
    "payload": {
        "time": 1000,
    }
}
```

Type: gameLoaded
---------

*gameLoaded* is used whenever the game finished loading. It's importand to measure loading performance of the slot.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "gameLoaded" | event type | **YES** |
| token     | String   | 95434fcf2653091660c7320 | game token | **YES** |
| payload     | Object   | {} | empty payload | **YES** |
| payload.time     | Number   | 1000 | time of milliseconds until the game was fully loaded | **YES** |

Type: gameError
---------

*gameError* is used whenever the game failed loading.

Additional *ErrorCodes* (please forward all error codes given from whow too)
| **Code** | **Meaning**
|----------|----------|
| 101     | Invalid payload/malformed response   |
| 102     | CURL/Protocol error   |
| 103     | timeout   |
| 104     | invalid gateway   |
| 105 | error 500 |
| 110 | invalid wallet |


#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "gameError" | event type | **YES** |
| token     | String   | 95434fcf2653091660c7320 | game token | **YES** |
| payload     | Object   | {} | empty payload | **YES** |
| payload.errorCode     | Number   | ErrorCodes | What error happened. Please see above | **YES** |
