**Publisher events**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [Events](#events)
  - [Example Message:](#example-message)
  - [Type: onGameLoaded](#type-ongameloaded)
      - [Parameters](#parameters)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  |
|-------------|------------|----------------------------------------------------|-----------|
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
    type: "gameLoaded",
    token: "95434fcf2653091660c7320",
    payload: {
        time: 1000,
    }
}
```

Type: onGameLoaded
---------

*gameLoaded* is used whenever the game finished loading. It's importand to measure loading performance of the slot.

#### Parameters

| **Name** | **Type** | **Example Value** | **Description** |  **Mandatory**   |
|----------|----------|-------------------|-----------------|------------------|
| type     | String   | "gameLoaded" | event type | **YES** |
| token     | String   | 95434fcf2653091660c7320 | game token | **YES** |
| payload     | Object   | {} | empty payload | **YES** |
| payload.time     | Number   | 1000 | time of milliseconds until the game was fully loaded | **YES** |
