**Whow X-Domain Events via postMessage**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [X-Domain Events](#x-domain-events)
	- [Type: startCasinoFreespins](#type-startcasinofreespins)
	- [Type: getGameSession](#type-getgamesession)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  |
|-------------|------------|----------------------------------------------------|-----------|
| 0.1.0       | 18.10.2016 | Initial version                                    | fschemmer |
| 0.1.1       | 13.03.2019 | Added getWallet                                    | mkercmar  |

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

Type: startCasinoFreespins
---------

*startCasinoFreespins* is used whenever the game has received casino freespins and is waiting for the casino to tell it to start them.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "startCasinoFreespins" | event type | **YES** |
| payload  | Object   | {} | empty payload | **YES** |

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
