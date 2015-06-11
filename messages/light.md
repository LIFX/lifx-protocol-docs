>  [LIFX Developer Home](http://developer.lifx.com) |  [LAN Protocol](README.md). LiFi Labs, Inc. © 2015. All rights reserved. Usage of this documentation is bound by the [LIFX Developer  Terms] (http://developer.lifx.com/terms.html).

# Light message types

## Index

* [Overview](#overview)
* [Message field data types](#message-field-data-types)
  * [HSBK](#hsbk)
* [Light messages](#light-messages) - message type value
  * [Get - 101](#get---101)
  * [SetColor - 102](#setcolor---102)
  * [State - 107](#state---107)
  * [GetPower - 116](#getpower---116)
  * [SetPower - 117](#setpower---117)
  * [StatePower - 118](#statepower---118)

## Overview

Light messages control and acquire the state of a specific type of device,
such as a lightbulb.  The state is comprised of the [label](device.md#label),
[power level](device.md#power-level) and [color](#hsbk).

There are a duplicate set of __device__ power related messages,
[Device::GetPower](device.md#getpower---20),
[Device::StatePower](device.md#statepower---22) and
[Device::SetPower](device.md#setpower---21),
which are extended for lights by adding a _duration_ field to
[Light::SetPower](#setpower---117).

The [Protocol header](../header.md#protocol-header) _message type_ field value
(decimal) appears after each message name in this document.
Each section below describes the _payload_ that is appended to the
[message header](../header.md).  Some messages do not require a payload.

## Message field data types

### HSBK

HSBK is used to represent the _color_ and _color_ temperature of a light.

The _color_ is represented as an HSB
([Hue, Saturation, Brightness](http://en.wikipedia.org/wiki/HSL_and_HSV))
value.

The _color temperature_ is represented in K
([Kelvin](http://en.wikipedia.org/wiki/Color_temperature))
and is used to adjust the warmness / coolness of a
white light, which is most obvious when saturation is close zero.

* Hue: range 0 to 65535
* Saturation: range 0 to 65535
* Brightness: range 0 to 65535
* Kelvin: range 2500° (warm) to 9000° (cool)

| Field | Type |
|-------|------|
| hue | unsigned 16-bit integer |
| saturation | unsigned 16-bit integer |
| brightness | unsigned 16-bit integer |
| kelvin | unsigned 16-bit integer |

For user interface purposes, the hue is typically scaled to between 0° and 360°.
Saturation and brightness are typically scaled to between 0% and 100%.

## Light messages

### Get - 101

Sent by a client to obtain the light state.
No payload required.
Causes the device to transmit a [State](#state---107) message.

### SetColor - 102

Sent by a client to change the light state.

| Field | Type |
|-------|------|
| reserved | unsigned 8-bit integer |
| color | HSBK |
| duration | unsigned 32-bit integer |

The _duration_ is the color transition time in milliseconds.

If the [Frame Address](../header.md#frame-address) _res\_required_ field is
set to one (1) then the device will transmit a [State](#state---107) message.

### State - 107

Sent by a device to provide the current light state.

| Field | Type |
|-------|------|
| color | HSBK |
| reserved | signed 16-bit integer |
| power | unsigned 16-bit integer |
| label | string, size: 32 bytes |
| reserved | unsigned 64-bit integer |

See [color](#hsbk), [label](device.md#label) and
[power level](device.md#power-level).

### GetPower - 116

Sent by a client to obtain the [power level](device.md#power-level).
No payload required.
Causes the device to transmit a [StatePower](#statepower---118) message.

### SetPower - 117

Sent by a client to change the light [power level](device.md#power-level).

| Field | Type |
|-------|------|
| level | unsigned 16-bit integer |
| duration | unsigned 32-bit integer |

The [power level](device.md#power-level) must be either 0 or 65535.

The _duration_ is the _power level_ transition time in milliseconds.

If the [Frame Address](../header.md#frame-address) _res\_required_ field is
set to one (1) then the device will transmit a
[StatePower](#statepower---118) message.

### StatePower - 118

Sent by a device to provide the current [power level](device.md#power-level).

| Field | Type |
|-------|------|
| level | unsigned 16-bit integer |
