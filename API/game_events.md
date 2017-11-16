**Whow API Game Events**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [Events](#events)
	- [Type: win](#type-win)
	- [Type: scatters](#type-scatters)
	- [Type: freespins](#type-freespins)
	- [Type: casinoFreespins](#type-casinofreespins)
	- [Type: bonusGameWin](#type-bonusgamewin)
	- [Type: betSizeChange](#type-betsizechange)
	- [Type: lineNumberChange](#type-linenumberchange)
	- [Type: autoSpin](#type-autospin)
	- [Type: fastSpin](#type-fastspin)
	- [Type: maxiPlay](#type-maxiplay)
	- [Type: fullRow](#type-fullrow)
	- [Type: symbols](#type-symbols)
	- [Type: special](#type-special)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  | **Approved** |
|-------------|------------|----------------------------------------------------|-----------|--------------|
| 0.1.0       | 13.06.2016 | Initial version                                    | fschemmer | -            |
| 0.1.1       | 18.10.2016 | Added casinoFreespins                              | fschemmer | -            |

Copyright
=========

Copyright © 2016 Whow Games GmbH. All rights reserved.

Introduction
============

This document serves as an explanation of different event types and when and how to use them. In case you are looking for a way to trigger events you can find the call documentation within the Whow API [documentation.md](https://github.com/whowgames/documentation/blob/master/API/documentation.md) document stored in our documentation repository on github.com.

GameEvents
======

GameEvents are used to publish specific events to the casino so the casino can notify the user in special cases or use the received event data for statistic purposes. Each event has a specific type which makes them distinguishable from each other.

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
| symbol     | Integer   | 4 | id of the scatter symbol | **YES** |
| positions | Array |[[],[0,1],[],[1],[2]] | position of scatters on the reels | **YES** |
| value    | Float(19.4) | 1250000 | win amount of scatter symbols | **YES** |

The positions array describes where on the reels the scatters are placed. Reels without scatter symbols are represented by an empty array "[]". Reels are always represented from left to right and top to bottom. Scatter positions always start with 0. In the example there are two scatters on the first and second position on the second reel and one scatter each on reel four, second from top, and five, third from top.

Type: freespins
---------------

*freespins* is used whenever the user has won freespins within the game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "freespins" | event type | **YES** |
| value    | Integer | 10 | amount of freespins gained | **YES** |
| betAmount | Integer | 12500 | bet amount with which the freespins are played | **YES** |
| inFreespins | Boolean | false | freespins won while playing freespins - defaults to false | **NO** |

Type: casinoFreespins
---------------------

*casinoFreespins* is used whenever the game triggers freespins which the game received on GameSessions::get

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "casinoFreespins" | event type | **YES** |
| value    | Integer | 10 | amount of freespins gained | **YES** |
| betAmount | Integer | 12500 | bet amount with which the freespins are played | **YES** |

Type: bonusGameWin
---------------------

*bonusGameWin* is used whenever the user has won chips within an bonus game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "bonusGameWin" | event type | **YES** |
| value    |  Float(19.4) | 1250000 | winning chip amount | **YES** |
| betAmount | Integer | 12500 | bet amount with which the bonus game is played | **YES** |

Type: betSizeChange
-------------------

*betSizeChange* is used whenever the user changes the bet size within the game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "betSizeChange" | event type | **YES** |
| value    | Float(19.4) | 12500 | new bet size | **YES** |

Type: lineNumberChange
----------------

*lineNumberChange* is used whenever the user changes the number of lines he's betting on within the game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "lineNumberChange" | event type | **YES** |
| value    | Integer | 25 | new line number | **YES** |

Type: autoSpin
----------------

*autoSpin* is used whenever the user toggles between auto spin on/off within the game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "autoSpin" | event type | **YES** |
| value    | Boolean | true | auto spin on or off | **YES** |

Type: fastSpin
----------------

*fastSpin* is used whenever the user toggles between fast spin on/off within the game.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "fastSpin" | event type | **YES** |
| value    | Boolean | true | fast spin on or off | **YES** |

Type: maxiPlay
----------------

*maxiPlay* is used whenever the user toggles between maxi play on/off within the game.
This event refers to a special game mode from Gamomat games.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "maxiPlay" | event type | **YES** |
| value    | Boolean | true | maxi play on or off | **YES** |

Type: fullRow
----------------

*fullRow* is used whenever the users wins an fullRow.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "fullRow" | event type | **YES** |
| value    | Integer | 5 | how many in a row | **YES** |
| wildcard    | Boolean | true | reached using wildcards (wilds) | **YES** |
| symbols    | Array | [0, 0, 1] | which symbols | **YES** |

Type: symbols
----------------

*symbols* is used to transmit all symbols in view port

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "symbols" | event type | **YES** |
| value    | Array | [1, 3, 5, 7, 9, 1, 2, 4, 6, 7, 8, 9, 10, 12, 14] | each element should represent a symbol number | **YES** |
| betAmount | Integer | 12500 | bet amount with which the bonus game is played | **YES** |

Type: special
----------------

*special* is used for game/publisher related special events

Current SubTypes are:
* expandingWild
* substitute
* winMultiplier
* stickyWild

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "symbols" | event type | **YES** |
| subType | String | "expandingWild" | name of the special event | **NO** |
| value    | Integer | 1 | event value | **YES** |
| betAmount | Integer | 12500 | bet amount with which the bonus game is played | **YES** |

