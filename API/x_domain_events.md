**Whow X-Domain Events via postMessage**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [X-Domain Events](#x-domain-events)
    - [Type: getGameSession](#type-getgamesession)
    - [Type: getWallet](#type-getwallet)
    - [Type: stopAutoSpin](#type-stopautospin)
    - [Type: startAutoSpin](#type-startautospin)

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

Type: stopAutoSpin
---

*stopAutoSpin* is used to give the casino the possibility to stop the auto spin mechanic in the games.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "stopAutoSpin" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |

#### Response

If this event is received please post an event by ourself to the "parent" of the IFrame.

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "autoSpinStopped" | event type | **YES** |
| payload     | Object   | {"wasInAutoSpin": true} | payload, with key wasInAutoSpin (boolean) informing if user was actually in Auto-Spin, or not. | **YES** |

Type: startAutoSpin
---

*startAutoSpin* is used to give the casino the possibility to start the auto spin mechanic in the game again.

This event is sent conjunction with stopAutoSpin to provide the option to pause autospin. Currently we do not plan to use this in other cases, so for now we don't need to manipulate bet size or limiting auto spins.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "startAutoSpin" | event type | **YES** |
| payload     | Object   | {} | empty payload | **YES** |
