>  [LIFX Developer Home](http://developer.lifx.com) |  [LAN Protocol](README.md). LiFi Labs, Inc. © 2015. All rights reserved. Usage of this documentation is bound by the [LIFX Developer  Terms] (http://developer.lifx.com/terms.html).

# Device message types

## Index

* [Overview](#overview)
* [Enumerated types](#enumerated-types)
  * [Service](#service)
* [Message field data types](#message-field-data-types)
  * [Label](#label)
  * [Port](#port)
  * [Power level](#power-level)
  * [Time](#time)
* [Device messages](#device-messages) - message type value
  * [GetService - 2](#getservice---2)
  * [StateService - 3](#stateservice---3)
  * [GetHostInfo - 12](#gethostinfo---12)
  * [StateHostInfo - 13](#statehostinfo---13)
  * [GetHostFirmware - 14](#gethostfirmware---14)
  * [StateHostFirmware - 15](#statehostfirmware---15)
  * [GetWifiInfo - 16](#getwifiinfo---16)
  * [StateWifiInfo - 17](#statewifiinfo---17)
  * [GetWifiFirmware - 18](#getwififirmware---18)
  * [StateWifiFirmware - 19](#statewififirmware---19)
  * [GetPower - 20](#getpower---20)
  * [SetPower - 21](#setpower---21)
  * [StatePower - 22](#statepower---22)
  * [GetLabel - 23](#getlabel---23)
  * [SetLabel - 24](#setlabel---24)
  * [StateLabel - 25](#statelabel---25)
  * [GetVersion - 32](#getversion---32)
  * [StateVersion - 33](#stateversion---33)
  * [GetInfo - 34](#getinfo---34)
  * [StateInfo - 35](#stateinfo---35)
  * [Acknowledgement - 45](#acknowledgement---45)
  * [GetLocation - 48](#getlocation---48)
  * [StateLocation - 50](#statelocation---50)
  * [GetGroup - 51](#getgroup---51)
  * [StateGroup - 53](#stategroup---53)
  * [EchoRequest - 58](#echorequest---58)
  * [EchoResponse - 59](#echoresponse---59)

## Overview

Device messages control and acquire the state of a device,
independent of its specific type.  The state is composed of the
[label](#label), [port](#port), [power level](#power-level), [time](#time),
firmware version and microcontroller statistics.

These messages are common to all LIFX devices, which may also implement
device specific messages, such as [light messages](light.md).

The [Protocol header](../header.md#protocol-header) _message type_ field value
(decimal) appears after each message name in this document.
Each section below describes the _payload_ that is appended to the
[message header](../header.md).  Some messages do not require a payload.

## Enumerated types

### Service

Describes the services exposed by the device.

| Type | Value |
|-----|------|
| UDP | 1 |

When a device is [discovered](../workflows.md#discovery) the Service types
and IP [port](#port) are provided.

The LIFX Protocol utilizes UDP/IP for all messages covered by this
documentation.

All values other than those documented above are reserved for future
protocol expansion.

## Message field data types

### Label

For user interfaces purposes, each device can be identified by their _label_.

| Field | Type |
|-------|------|
| label | string, size: 32 bytes |

The _label_ string is a fixed-length field, which is __not__ null-terminated.

### Port

The IP port number used by a LIFX device for a specific [Service](#service).

| Field | Type |
|-----|------|
| port | unsigned 32-bit integer |

Whilst LIFX devices usually listen to port 56700, it is strongly recommended
that devices are discovered and the provided port number is used.

As of LIFX Protocol V2, LIFX devices sending unicast message responses will
reply to whichever port number the client has bound.
See [message header frame source identifier](../header.md#frame)
regarding the conditions under which unicast messages are sent.

Prior to LIFX Protocol V2, i.e the Original LIFX A21 lightbulb running
firmware version 1.x, LIFX devices send broadcast message responses to
port 56700.

To get the best compatibility across both newer and older LIFX devices,
clients should bind to UDP port 56700.

### Power level

The _power level_ can be either standby (0) or enabled (65535).

| Field | Type |
|-------|------|
| level | unsigned 16-bit integer |

Currently, only the values 0 and 65535 are supported

### Time

All _time_ values have a precision of nanoseconds.
When an absolute time value is provided, then it is the number of
nanoseconds since the [epoch](http://en.wikipedia.org/wiki/Unix_time),
i.e Thursday 1st January 1970 00:00:00.

| Field | Type |
|-----|------|
| time | unsigned 64-bit integer |

## Device messages

### GetService - 2

Sent by a client to acquire responses from all devices on the local network.
No payload is required.
Causes the devices to transmit a [StateService](#stateservice---3) message.

When using this message, the [Frame](../header.md#frame) _tagged_ field must
be set to one (1).

### StateService - 3

Response to [GetService](#getservice---2) message.

Provides the device [Service](#service) and [port](#port).
If the Service is temporarily unavailable, then the _port_ value will be 0.

| Field | Type |
|-----|------|
| service | unsigned 8-bit integer, maps to [Service](#service) |
| port | unsigned 32-bit integer |

### GetHostInfo - 12

Get Host MCU information.
No payload is required.
Causes the device to transmit a [StateHostInfo](#statehostinfo---13) message.

### StateHostInfo - 13

Response to [GetHostInfo](#gethostinfo---12) message.

Provides host MCU information.

* signal: radio receive signal strength in milliWatts
* tx: bytes transmitted since power on
* rx: bytes received since power on

| Field | Type |
|-------|------|
| signal | 32-bit float |
| tx | unsigned 32-bit integer |
| rx | unsigned 32-bit integer |
| reserved | signed 16-bit integer |

### GetHostFirmware - 14

Gets Host MCU firmware information.
No payload is required.
Causes the device to transmit a [StateHostFirmware](#statehostfirmware---15)
message.

### StateHostFirmware - 15

Response to [GetHostFirmware](#gethostfirmware---14) message.

Provides host firmware information.

* build: firmware build [time](#time) (absolute time in nanoseconds since epoch)
* version: firmware version

| Field | Type |
|-------|------|
| build | unsigned 64-bit integer |
| reserved | unsigned 64-bit integer |
| version | unsigned 32-bit integer |

### GetWifiInfo - 16

Get Wifi subsystem information.
No payload is required.
Causes the device to transmit a [StateWifiInfo](#statewifiinfo---17) message.

### StateWifiInfo - 17

Response to [GetWifiInfo](#getwifiinfo---16) message.

Provides Wifi subsystem information.

* signal: radio receive signal strength in mw
* tx: bytes transmitted since power on
* rx: bytes received since power on

| Field | Type |
|-------|------|
| signal | 32-bit float |
| tx | unsigned 32-bit integer |
| rx | unsigned 32-bit integer |
| reserved | signed 16-bit integer |

### GetWifiFirmware - 18

Get Wifi subsystem firmware.
No payload is required.
Causes the device to transmit a [StateWifiFirmware](#statewififirmware---19)
message.

### StateWifiFirmware - 19

Response to [GetWifiFirmware](#getwififirmware---18) message.

Provides Wifi subsystem information.

* build: firmware build [time](#time) (absolute time in nanoseconds since epoch)
* version: firmware version

| Field | Type |
|-------|------|
| build | unsigned 64-bit integer |
| reserved | unsigned 64-bit integer |
| version | unsigned 32-bit integer |

### GetPower - 20

Get device [power level](#power-level).
No payload is required.
Causes the device to transmit a [StatePower](#statepower---22) message.

### SetPower - 21

Set device [power level](#power-level). 

Zero implies standby and non-zero sets a corresponding power draw level.
Currently only 0 and 65535 are supported.

| Field | Type |
|-------|------|
| level | unsigned 16-bit integer |

### StatePower - 22

Response to [GetPower](#getpower---20) message.

Provides device [power level](#power-level).

| Field | Type |
|-------|------|
| level | unsigned 16-bit integer |

### GetLabel - 23

Get device [label](#label).
No payload is required.
Causes the device to transmit a [StateLabel](#statelabel---25) message.

### SetLabel - 24

Set the device [label](#label) text.

| Field | Type |
|-------|------|
| label | string, size: 32 bytes |

### StateLabel - 25

Response to [GetLabel](#getlabel---23) message.

Provides device [label](#label).

| Field | Type |
|-------|------|
| label | string, size: 32 bytes |

### GetVersion - 32

Get the hardware version.
No payload is required.
Causes the device to transmit a [StateVersion](#stateversion---33) message.

### StateVersion - 33

Response to [GetVersion](#getversion---32) message.

Provides the hardware version of the device.

* vendor: vendor ID
* product: product ID
* version: hardware version

| Field | Type |
|-------|------|
| vendor | unsigned 32-bit integer |
| product | unsigned 32-bit integer |
| version | unsigned 32-bit integer |

### GetInfo - 34

Get run-time information.
No payload is required.
Causes the device to transmit a [StateInfo](#stateinfo---35) message.

### StateInfo - 35

Response to [GetInfo](#getinfo---34) message.

Provides run-time information of device.

* time: current [time](#time) (absolute time in nanoseconds since epoch)
* uptime: [time](#time) since last power on (relative time in nanoseconds)
* downtime: last power off period, 5 second accuracy (in nanoseconds)

| Field | Type |
|-------|------|
| time | unsigned 64-bit integer |
| uptime | unsigned 64-bit integer |
| downtime | unsigned 64-bit integer |

### Acknowledgement - 45

Response to any message sent with _ack\_required_ set to 1.
See [message header frame address](../header.md#frame-address).

### GetLocation - 48

Ask the bulb to return its location information.
No payload is required.
Causes the device to transmit a [StateLocation](statelocation---50) message.

### StateLocation - 50

Device location.

| Field | Type |
|-------|------|
| location | byte array, size: 16 |
| label | string, size: 32 |
| updated_at | unsigned 64-bit integer |

### GetGroup - 51

Ask the bulb to return its group membership information.
No payload is required.
Causes the device to transmit a [StateGroup](stategroup---53) message.

### StateGroup - 53

Device group.

| Field | Type |
|-------|------|
| group | byte array, size: 16 |
| label | string, size: 32 |
| updated_at | unsigned 64-bit integer |

### EchoRequest - 58

Request an arbitrary payload be echoed back.
Causes the device to transmit an [EchoResponse](#echoresponse---59) message.

| Field | Type |
|-------|------|
| payload | byte array, size: 64 bytes |

### EchoResponse - 59

Response to [EchoRequest](#echorequest---59) message.

Echo response with payload sent in the EchoRequest.

| Field | Type |
|-------|------|
| payload | byte array, size: 64 bytes |
