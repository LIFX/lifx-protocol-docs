>  [LIFX Developer Home](http://developer.lifx.com) |  [LAN Protocol](README.md). LiFi Labs, Inc. © 2015. All rights reserved. Usage of this documentation is bound by the [LIFX Developer  Terms] (http://developer.lifx.com/terms.html).

# LIFX LAN Protocol V2

## Table of contents

* [Overview](#overview)
* [Transport layer](#transport-layer)
* [Message description](#message-description)
* [Limits](#limits)
* [License](#license)
* [Disclaimer](#disclaimer)
* Message details
  * [Header description](header.md)
  * [Workflow diagrams](workflows.md)
* Message types
  * [Device messages](messages/device.md)
  * [Light messages](messages/light.md)
* [Documentation tools](tools.md)

## Overview

This documentation is intended for third-party developers creating
client applications, which interact with LIFX lightbulbs over the LAN
by sending and receiving, then parsing LIFX Protocol messages.

The LIFX Protocol consists of messages that are used to control and obtain
the state of devices (specifically lightbulbs).

Each message is composed of a header and a payload.

Headers contain the message size, type, routing, response flags and more.
Payloads contain information germane to a specific message type.

To interact with LIFX lightbulbs over the Internet (Cloud or WAN),
see the [LIFX HTTP API documention](http://developer.lifx.com).

## Transport layer

The LIFX Protocol utilizes UDP/IP for all messages covered by this
documentation.

Numeric data-type byte-order is
[little-endian](http://en.wikipedia.org/wiki/Endianness#Little-endian)

## Message description

All LIFX Protocol messages start with the header, which is covered in the
[Header description](header.md).

The header contains the message type field.  There are two categories
of messages detailed in this documentation, (1) messages understood by
all devices* and (2) those messages specific to lightbulbs.

The message type determines the format of payload.
The various message payloads are covered in the
[Device](messages/device.md) and [Light](messages/light.md)
messages documentation.

Most interactions consist of a number of messages transmitted between the
client and the device.  Some common message exchanges are shown in the
[Workflow diagrams](workflows.md).

Often, device discovery is a good place to start.
In which case, the [discovery workflow](workflows.md#discovery),
along with the [Device::GetService](messages/device.md#getservice---2)
and the [Device::StateService](messages/device.md#stateservice---3)
messages are relevant.

_\* Protocol designers like to think ahead.  Don't get too excited ...
LIFX only manufactures and sells network connected lightbulbs !_

## Limits

* Maximum recommended message transmit rate to a device: 20 per second


## Disclaimer

Using undocumented message types or field values, sending poorly formed
messages or excessive message rates to LIFX devices and/or LIFX services
may result in unexpected device or protocol message behavior.
