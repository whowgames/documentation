**Whow X-Domain Events via postMessage**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [X-Domain Events](#x-domain-events)
    - [Example Implementation](#example-implementation)
    - [Type: getGameSession](#type-getgamesession)
    - [Type: getWallet](#type-getwallet)
    - [Type: pauseAutoSpin](#type-pauseautospin)
    - [Type: resumeAutoSpin](#type-resumeautospin)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  |
|-------------|------------|----------------------------------------------------|-----------|
| 0.1.0       | 18.10.2016 | Initial version                                    | fschemmer |
| 0.1.1       | 13.03.2019 | Added getWallet                                    | mkercmar  |
| 0.2.0       | 15.03.2019 | Removed startCasinoFreespins, add auto spin controll messages | mkercmar |

Copyright
=========

Copyright © 2019 Whow Games GmbH. All rights reserved.

Introduction
============

This document serves as an explanation of different x-domain-events published to the game via Java Script's postMessage function.

X-Domain Events
======

X-Domain Events are used to publish specific events to the game so the casino can notify the game in special cases so that it can react accordingly. Each event has a specific type which makes them distinguishable from each other.

All events **must have** the paramter *type* and *payload*.

On web platform the events are sent via postMessage API: https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage

On mobile platform the game needs to expose a global JavaScript Function called 'mobileAppDomainEvent'.

Example Implementation:
---------

```html
<!doctype html>
<html>
<head>
    <script>
    function mobileAppDomainEvent(message) {
        // DATA
        console.log(message);
    }

    window.addEventListener("message", receiveMessage, false);

    function receiveMessage(event) {
        // DATA
        console.log(event.data);
    }
    </script>
</head>
<body>

<h1>SLOT CONTENT</h1>

</body>
</html>
```

Type: getGameSession
---------

*getGameSession* is used whenever the game should perform [GameSessions::get](https://github.com/whowgames/documentation/blob/master/API/documentation.md#gamesessionsget) again.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "getGameSession" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |

Type: getWallet
---------

*getWallet* is used whenever the game should perform [GameSessions::wallet](https://github.com/whowgames/documentation/blob/master/API/documentation.md#gamesessionswallet) to receive current user wallet amount.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "getWallet" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |

Type: pauseAutoSpin
---

*pauseAutoSpin* is used to give the casino the possibility to stop current auto spin flow in the slot.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "pauseAutoSpin" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |

Type: resumeAutoSpin
---

*resumeAutoSpin* is used to give the casino the possibility to resume a paused auto spin flow in the slot.

This event is sent in conjunction with pauseAutoSpin, and should only have an effect on the slot if there been paused auto spins.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "resumeAutoSpin" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |
