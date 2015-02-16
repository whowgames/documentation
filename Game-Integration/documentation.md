**Game Integration Guide**

Table of Contents
=================

- [Table of Contents](#table-of-contents)
- [Revision History](#revision-history)
- [Copyright](#copyright)
- [Introduction](#introduction)
- [Getting started](#getting-started)
- [GET Parameters](#get-parameters)
- [Resolutions](#resolutions)

Revision History
================

| **Version** | **Date**   | **Changes**                                        | **Name**  | **Approved** |
|-------------|------------|----------------------------------------------------|-----------|--------------|
| 0.1      | 05.09.2014 | Initial version | fschemmer | -            |
| 0.2       | 15.09.2014 | Added easyXDM sound | fschemmer | -            |
| 0.3       | 13.10.2014 | Added new bets | fschemmer | -            |
| 0.4       | 16.02.2015 | Updated document from .docX -> .md, removed easyXDM | fschemmer | -        |

Copyright
=========

Copyright © 2014 - 2015 Whow Games GmbH. All rights reserved.

Introduction
============

The REST API serves as an API to connect your games and services to the Whow backend.  
This document is intended for software developers and other technical positions.

Getting started
===============

To integrate your game into our casino applications you need to provide us with an URI to your games location. Your game will then be loaded via an IFrame into our casinos. Additional arguments are passed via GET parameters to your game. Within the GET parameter is a game token which will later on be used by your game to to authenticate yourself & communicate with our REST API.

GET Parameters
==============
When your game is loaded via easyXDM we will append some GET parameters to the URI which you need to parse and react to.  
Your game URI might look like this then:

>https://yourCompany.com/myGame?token= 54e1fd3dd4b9a4653c8b4567&sound=0&locale=de_DE

The parameters we are providing to you have different use cases. Next is a list of what parameters might be present – they don’t have to – and what they are used for.

| **Name** | **Type** | **Example Value**        | **Description**      |
|----------|----------|--------------------------|----------------------|
| token | String   | 54e1fd3dd4b9a4653c8b4567 | token you receive to authenticate yourself at the REST API |
| sound | Integer | 0 | indicates if sound is turned off (0) or on (1) |
| locale | Char(5) | de_DE | language code (ISO 639) + \"\_\" + geo code (ISO 3166) |

Resolutions
==========
To give the user the best experience possible we have some game resolution restrictions in place which you should stick to if possible. If not possible please contact us we will find a solution together.

> Max Resolution
>
>     1182x760

> Min Resolution
>
>     902x580