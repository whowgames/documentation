**REST API Events**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [Events](#events)
	- [Type: win](#typewin)
	- [Type: scatters](#typescatters)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  | **Approved** |
|-------------|------------|----------------------------------------------------|-----------|--------------|
| 0.1.0       | 13.06.2016 | Initial version                                    | fschemmer | -            |

Copyright
=========

Copyright © 2016 Whow Games GmbH. All rights reserved.

Introduction
============

This document serves as an explanation of different event types and when and how to use them. In case you are looking for a way to trigger events you can find the call documentation within the REST API [documentation.md](https://github.com/whowgames/documentation/blob/master/REST-API/documentation.md) file stored in our documentation repository on github.com.

Events
======

Events are used to publish specific game events to the casino so the casino can notify the user in special cases or use the received event data for statistic purposes. Each event has a specific type which makes them distinguishable from each other.

All events **must have** the paramter *type* and *value*. Additional to these two mandatory parameters events sometimes define additional paramters which might be mandatory as well. So read the documentation for each event carefully.

Type: win
---------

*win* is used whenever you have a big win that you want to publish to the casino. In case your game specifies different win/bet ratios for example as high, big, monster or legendary wins you can add this information within the winType field.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "bet" | event type | **YES** |
| betAmount | Float(19.4) | 1250 | chip amount bet to achieve this win amount | **YES** |
| value    | Float(19.4) | 1250000 | winning chip amount | **YES** |
| winType | String | "legendary" | displayed type of the win within the slot | **NO** |

Type: scatters
---------

*scatters* is used whenever you have a win triggered by scatter symbol and you want to publish this event to the casino.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "scatters" | event type | **YES** |
| positions | Array |[[],[0,1],[],[1],[2]] | position of scatters on the reels | **YES** |
| value    | Float(19.4) | 1250000 | win amount of scatter symbols | **YES** |

The positions array describes where on the reels the scatters are placed. Reels without scatter symbols are represented by an empty array "[]". Reels are always represented from left to right and top to bottom. Scatter positions always start with 0. In the example there are two scatters on the first and second position on the second reel and one scatter each on reel four, second from top, and five, third from top.



