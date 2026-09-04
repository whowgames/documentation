# Slot Control Messaging Protocol

This document defines the communication protocol enabling remote control of a slot game embedded inside an HTML wrapper. Communication occurs exclusively through window.postMessage. The slot must process incoming JACKPOT: events and emit the corresponding JACKPOT: state updates.

# **1. Message Format**

All events must follow the structure shown below. The payload field is optional and only included when needed.

```
{  
  "type": "JACKPOT:EVENT_NAME",  
  "payload": { ... }  
}
```

Messages are sent using:

```
window.parent.postMessage(messageObject, '*');
```

Messages are received using:

```
window.addEventListener("message", (event) => {  
  const msg = event.data;  
  if (!msg || !msg.type) return;  
});
```

# **2. Events the Slot Must Send (Publisher → Wrapper)**

## **2.1 JACKPOT:SLOT_RENDERED**

Indicates that the slot has finished loading all internal assets, logic, and UI, and that the first frame has been rendered on screen.

This event must be sent **before** any introduction, splash, tutorial, or confirmation screen is displayed, and must **not** wait for the user to dismiss such a screen. The wrapper uses it exclusively to measure loading time; it is not a trigger for external UI controls.

Slots without an introduction screen must send this event as well, immediately followed by JACKPOT:SLOT_LOADED.

```
{  
  "type": "JACKPOT:SLOT_RENDERED"  
}
```

## **2.2 JACKPOT:SLOT_LOADED**

Indicates that the slot is ready for user interaction, i.e. that any introduction, splash, tutorial, or confirmation screen has been dismissed and the player can operate the reels. This event is the trigger to display external UI controls, and is the reference point for the initialization events described in 2.6, 2.9 and 2.10.

This event is always preceded by JACKPOT:SLOT_RENDERED. If the slot has no introduction screen, both events are sent back to back.

```
{  
  "type": "JACKPOT:SLOT_LOADED"  
}
```

## **2.3 JACKPOT:AUTOSPIN**

Indicates that the user has enabled or disabled autoplay within the slot UI.

```
{  
  "type": "JACKPOT:AUTOSPIN",  
  "payload": { "enabled": true }  
}
```

## **2.4 JACKPOT:FAST_MODE**

Indicates that the fast mode state inside the slot has changed. The payload reflects the resulting fast mode state.

```
{  
  "type": "JACKPOT:FAST_MODE",  
  "payload": { "enabled": true }  
}
```

## **2.5 JACKPOT:SOUND**

Indicates that the sound state inside the slot has changed. If multiple sound channels exist, enabled must be false only if all channels are disabled.

```
{  
  "type": "JACKPOT:SOUND",  
  "payload": { "enabled": true }  
}
```

## **2.6 JACKPOT:UPDATE_BETSIZE**

Sent whenever the user changes the bet size through the slot UI. It is expected that this event is also sent once after the JACKPOT:SLOT_LOADED event, to initialize the remote control with the current bet size.

```
{  
  "type": "JACKPOT:UPDATE_BETSIZE",  
  "payload": { "value": 200000 }  
}
```

## **2.7 JACKPOT:SPIN_START**

Indicates that the reels have started spinning.

```
{  
  "type": "JACKPOT:SPIN_START"  
}
```

## **2.8 JACKPOT:SPIN_COMPLETE**

Indicates that the spin cycle has completed and the reels have stopped.

```
{  
  "type": "JACKPOT:SPIN_COMPLETE"  
}
```

## **2.9 JACKPOT:UPDATE_PAYLINES** 

Sent, when the slot allows the user to change the paylines. **Important**: if the slot is expecting to receive bet sizes per line, it is also expected that this event is sent once after the JACKPOT:SLOT_LOADED event, to initialize the remote control with the default number of paylines (regardless of whether the paylines can be changed in the slot).

```
{  
  "type": "JACKPOT:UPDATE_PAYLINES",  
  "payload": { "value": 5 }  
}
```

## **2.10 JACKPOT:UPDATE_EXTRABET** 

Sent, when the slot has an “extra bet” feature, that adds on top of the default bet size. It is expected that this event is also sent once after the JACKPOT:SLOT_LOADED event, to initialize the remote control with the default extra bet value.

```
{  
  "type": "JACKPOT:UPDATE_EXTRABET",  
  "payload": { "value": 200000 }  
}
```

# **3. Events the Slot Must Handle (Wrapper → Publisher)**

## **3.1 JACKPOT:SPIN**

Requests the slot to perform a regular spin action.

```
{  
  "type": "JACKPOT:SPIN"  
}
```

## **3.2 JACKPOT:AUTOSPIN**

Requests enabling or disabling autoplay. The slot must confirm via JACKPOT:AUTOSPIN.

```
{  
  "type": "JACKPOT:AUTOSPIN",  
  "payload": { "enabled": true }  
}
```

## **3.3 JACKPOT:FAST_MODE**

Requests enabling or disabling fast mode. The slot must confirm via JACKPOT:FAST_MODE.

```
{  
  "type": "JACKPOT:FAST_MODE",  
  "payload": { "enabled": true }  
}
```

## **3.4 JACKPOT:SOUND**

Requests enabling or disabling sound. If multiple channels exist, all channels must follow the enabled flag.

```
{  
  "type": "JACKPOT:SOUND",  
  "payload": { "enabled": true }  
}
```

## **3.5 JACKPOT:BETSIZE_INCREASE**

Requests increasing the bet size by one step. The slot should emit a JACKPOT:UPDATE_BETSIZE with the new bet size afterwards. If the user is in a freespin game or any other bonus round, during which the bet size can not be changed, the slot should ignore the event and just return an JACKPOT:UPDATE_BETSIZE with the current bet size.

```
{  
  "type": "JACKPOT:BETSIZE_INCREASE"  
}
```

## **3.6 JACKPOT:BETSIZE_DECREASE**

Requests decreasing the bet size by one step. The slot should emit a JACKPOT:UPDATE_BETSIZE with the new bet size afterwards. If the user is in a freespin game or any other bonus round, during which the bet size can not be changed, the slot should ignore the event and just return an JACKPOT:UPDATE_BETSIZE with the current bet size.

```
{  
  "type": "JACKPOT:BETSIZE_DECREASE"  
}
```

## **3.7 JACKPOT:MAXBET**

Requests setting the bet size to the maximum allowed value. The slot should emit a JACKPOT:UPDATE_BETSIZE with the new bet size afterwards. If the user is in a freespin game or any other bonus round, during which the bet size can not be changed, the slot should ignore the event and just return an JACKPOT:UPDATE_BETSIZE with the current bet size.

```
{  
  "type": "JACKPOT:MAXBET"  
}
```

# **4. Optional Events the Slot *Should* Handle (Wrapper → Publisher)** 

## **4.1 JACKPOT:BETSIZE** 

Sets the betsize to the value provided in the payload. The slot should emit a JACKPOT:UPDATE_BETSIZE with the new bet size afterwards. If the user is in a freespin game or any other bonus round, during which the bet size can not be changed, the slot should ignore the event and just return an JACKPOT:UPDATE_BETSIZE with the current bet size.

```
{  
  "type": "JACKPOT:BETSIZE",  
  "payload": { "value": bigint}  
}
```

## **4.2 JACKPOT:TOGGLE_UI** 

Hide or show the slot controls (like spin button, etc) in the main slot UI. 

```
{  
  "type": "JACKPOT:TOGGLE_UI"  
}
```

# **5. Optional Events the Slot *Should* Send (Publisher → Wrapper)**

## **5.1 JACKPOT:GAME_ERROR**

Sent, whenever there is an error during slot processing. Please provide one of the codes from the table below. If you think there is a case which is not covered by the existing codes, please contact us and we’ll update the table accordingly.

| Code | Meaning |
| ----- | ----- |
| 101 | Invalid payload/malformed response from GameSessions-API |
| 102 | Other CURL/Protocol errors, such as SSL issues |
| 103 | Timeout of publisher backend |
| 104 | Invalid gateway / GameSessions-API timeout |
| 105 | GameSessions-API returned error 500 while processing request |
| 110 | Invalid wallet returned by GameSessions-API |

```
{  
  "type": "JACKPOT:GAME_ERROR",  
  "payload": { "code": 102 }  
}
```
