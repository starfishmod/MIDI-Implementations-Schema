# MIDI Implementations Schema


#### Version 0.9

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

The MIS specification is licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

## Introduction

MIDI Implementations Schema (MIS) is a project used to describe and document MIDI Implementations.

The MIS specification defines a set of files required to describe such an implementation. These files can then be used by software to generate clients in various languages. Additional utilities can also take advantage of the resulting files, such as testing and documentation tools.

This schema attempts to follow the MIDI Implementation Chart V 2.0 (MMA/AMEI RP-028) as closely as possible. This document is available at [MIDI Association](https://www.midi.org/).

## Revision History

Version | Date | Notes
--- | --- | ---
0.9 | 2016-08-02 | First release of the MIS Specification for comment and review

## Definitions

##### <a name="MIDI"></a>MIDI
The specification as defined by the [MIDI Association](https://www.midi.org/)

##### <a name="sysex"></a>Sysex
[System Exclusive messages](https://en.wikipedia.org/wiki/MIDI#System_Exclusive_messages)

##### <a name="cc"></a>Control Change 
Used to send changes on a specific channel. Examples include pan, volume.

##### <a name="nrpn"></a>NRPN 
Non-Registered Paramter Number. More often than not manufactuers use [NRPN](https://en.wikipedia.org/wiki/NRPN) to send data to and from synthesizers. NRPN usually require between 3 and 4  -  3 byte MIDI messages.

##### <a name="deviceEnquiry"></a>Device Enquiry 
A standard Sysex message that may return information about the device.

## Specification

### Format

The files describing the implementation in accordance with the MIS specification are represented as JSON objects and conform to the JSON standards. 

For example, if a field is said to have an array value, the JSON array representation will be used:

```js
{
   "field" : [...]
}
```

All field names in the specification are **case sensitive**.

The schema exposes two types of fields. Fixed fields, which have a declared name, and Patterned fields, which declare a regex pattern for the field name. Patterned fields can have multiple occurrences as long as each has a unique name. 

### File Structure

The MIS representation of the implementation is made of a single file. 

By convention, the MIS specification file is named based on the manufacter and model number `Korg_ES1.json`.

### Data Types

Primitive data types in the MIS Specification are based on the types supported by the [JSON-Schema Draft 4](http://json-schema.org/latest/json-schema-core.html#anchor8). Models are described using the [Schema Object](#schemaObject) which is a subset of JSON Schema Draft 4.

<a name="hexAndDec"></a>Hexidecimal and integers can be used interchangably. However numbers in Hexidecimal should be proceeded with `0x` and must become strings as JSON does not support Hex. So the number 66 is represented as `"0x42"`. This makes it clear for any humans to distinguish between the two - but does put extra burden on the computer to do the same.

### Schema

#### <a name="misObject"></a>MIS Object

This is the root document object for the implementation specification.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="misMis"></a>MIS | `string` | **Required.** Specifies the MIS Specification version being used. The value MUST be `"0.9"`.
<a name="misInfo"></a>info | [Info Object](#infoObject) | **Required.** Provides metadata about the implementation. The metadata can be used by the software if needed.
<a name="misChart"></a>chart | [Chart Object](#chartObject) | **Required.** This provides the basic implementation information.
<a name="misControllers"></a>controllers | [Controllers Object](#controllersObject) | This provides the information to transmit or receive Controller information via [CC](#cc), RPN or [NRPN](#nrpn).
<a name="misSysex"></a>sysex | [Sysex Object](#sysexObject) | This provides the information to transmit or receive [Sysex](#sysex) Information.


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="misExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

#### <a name="infoObject"></a>Info Object

The object provides metadata about the implementation. 

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="infoManufacturer"></a>manufacturer | [nameID Object](#nameIDObject) | **Required.** The manufactuer of the device. The ID refers to the ID as listed [MANUFACTURER SYSEX ID NUMBERS](https://www.midi.org/specifications/item/manufacturer-id-numbers) and the [Device Enquiry](#deviceEnquiry)
<a name="infoFamily"></a>family | [nameID Object](#nameIDObject) | The family of the device. The id refers to data from the [Device Enquiry](#deviceEnquiry)
<a name="infoFamily"></a>model | [nameID Object](#nameIDObject) | **Required.** The model of the device. The id refers to data from the [Device Enquiry](#deviceEnquiry)
<a name="infoDate"></a>date | `string` | **Required.** The date of this MIS.
<a name="infoDocumentVersion"></a>documentVersion | `string` | The version of this MIS.
<a name="infoDeviceVersions"></a>deviceVersions | [`string`] | A list of model versions that this document refers to. This should be calculated from the [Device Enquiry](#deviceEnquiry)
<a name="infoAuthor"></a>author | `string` | The original author of this implementation.
<a name="infoContributors"></a>contributors | [`string`] | Other authors who have worked on this implementation

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="infoExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Info Object Example:

```js
{
    "manufacturer": {
      "name": "Korg",
      "id": 66
    },
    "family": {
      "name": "Electribe",
      "id": 0
    },
    "model": {
      "name": "ES-1",
      "id": 87
    },
    "date": "1999-01-01",
    "deviceVersions": [
      "1.6"
    ],
    "documentVersion": 0.2,
    "author": "Andrew Mee <primary.edw@gmail.com>",
    "contributors": []
}
```


#### <a name="nameIDObject"></a>nameID Object

Simple object for holding a name and ID value.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="nameIDName"></a>name | `string` | **Required.** The identifying name of the object.
<a name="nameIDID"></a>id | `integer` | The id of the name used as reference

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="nameIDExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### nameID Object Example:

```js
{
  "name": "Korg",
  "id": 66
}
```


#### <a name="chartObject"></a>Chart Object

This describes the basic MIDI implementation of the device.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="chartMidiChannels"></a>midiChannels | [RTMultiRange Object](#RTMultiRangeObject) | The range of MIDI channels that the device transmits, exports, responds to, and/or imports. Devices using extended channel systems via multiple cables or input/output ports should list the total number of channels in the appropriate “Transmitted” or “Recognized” columns and should use the “Remarks” column to indicate the terminology used by the device to identify the extra channels (i.e., “A1 - A16, B1 - B-16”)
<a name="chartNoteNumbers"></a>noteNumbers | [RTRange Object](#RTRangeObject) | The total range of transmitted or recognized notes. 
<a name="chartProgramChange"></a>programChange | [RTRange Object](#RTRangeObject) | Indicate the range of Program Change numbers which are transmitted and/or recognized. If not implemented, enter a “No” in the appropriate column.
<a name="chartBankSelect"></a>bankSelect | [RTBoolean Object](#RTBooleanObject) | Use a “Yes” or “No” to indicate whether or not the device correctly responds to Bank Select messages as per the MIDI 1.0 Specification. Devices that respond only to Bank Select MSB (cc #0) but not to the LSB (cc #32) should place a "No" in the “Recognized” column and should indicate this in the “Remarks” column. If the device does correctly respond to Bank Select messages, use the “Remarks” column to indicate what banks or ranges of banks are available in the device. If certain banks are accessible only by MIDI (and not by front panel user control), these should be listed in the “Remarks” column.
<a name="chartMode1"></a>mode1 | [RTBoolean Object](#RTBooleanObject) | Mode 1: Omni-On, Poly (Yes/No)
<a name="chartMode2"></a>mode2 | [RTBoolean Object](#RTBooleanObject) | Mode 2: Omni-On, Mono (Yes/No)
<a name="chartMode3"></a>mode3 | [RTBoolean Object](#RTBooleanObject) | Mode 3: Omni-Off, Poly (Yes/No) 
<a name="chartMode4"></a>mode4 | [RTBoolean Object](#RTBooleanObject) | Mode 4: Omni-Off, Mono (Yes/No) 
<a name="chartModeMulti"></a>modeMulti | [RTBoolean Object](#RTBooleanObject) | Multi Mode (Yes/No)
<a name="chartNoteOnVelocity"></a>noteOnVelocity | [RTBoolean Object](#RTBooleanObject) | Note On Velocity (Yes/No)
<a name="chartNoteOffVelocity"></a>noteOffVelocity | [RTBoolean Object](#RTBooleanObject) | Note Off Velocity (Yes/No)
<a name="chartChannelAftertouch"></a>channelAftertouch | [RTBoolean Object](#RTBooleanObject) | Channel AfterTouch (Yes/No)
<a name="chartkeyAftertouch"></a>keyAftertouch | [RTBoolean Object](#RTBooleanObject) | Poly (Key) Aftertouch (Yes/No) 
<a name="chartPitchBend"></a>pitchBend | [RTBoolean Object](#RTBooleanObject) | Pitch Bend (Yes/No) 
<a name="chartActiveSense"></a>activeSense | [RTBoolean Object](#RTBooleanObject) | Active Sensing (Yes/No) 
<a name="chartSytemReset"></a>systemReset | [RTBoolean Object](#RTBooleanObject) | System Reset (Yes/No) 
<a name="chartTuneRequest"></a>tuneRequest | [RTBoolean Object](#RTBooleanObject) | Tune Request (Yes/No)  
<a name="chartMidiClock"></a>midiClock | [RTBoolean Object](#RTBooleanObject) | MIDI Clock (Yes/No)  
<a name="chartSongPos"></a>songPos | [RTBoolean Object](#RTBooleanObject) | Song Position Pointer (Yes/No)  
<a name="chartSongSelect"></a>songSelect | [RTBoolean Object](#RTBooleanObject) | Song Select (Yes/No)  
<a name="chartStart"></a>start | [RTBoolean Object](#RTBooleanObject) | Start (Yes/No)  
<a name="chartContinue"></a>continue | [RTBoolean Object](#RTBooleanObject) | Continue (Yes/No)   
<a name="chartStop"></a>stop | [RTBoolean Object](#RTBooleanObject) | Stop (Yes/No)   
<a name="chartMTC"></a>MTC | [RTBoolean Object](#RTBooleanObject) | MIDI Time Code (Yes/No)  
<a name="chartMMC"></a>MMC | [RTBoolean Object](#RTBooleanObject) | MIDI Machine Control (Yes/No)   
<a name="chartGM"></a>GM | [`string`] | Indicate whether or not the device has a mode of operation which complies with any of the General MIDI specifications: General MIDI System Level 1 (GM), General MIDI System Level 2 (GM2) and/or General MIDI Lite (GM Lite). 
<a name="chartDLS"></a>DLS | [`string`] | Indicate whether or not the device has a mode of operation that complies with any of the Downloadable Sounds specifications: DLS Level 1 (DLS) , DLS Level 2 (DLS2, including DLS 2.1 and DLS 2.2), and/or Mobile DLS.  It is recommended that manufacturers indicate in the Remarks column the means of receiving DLS data (i.e., specific physical format, device interface, or transport protocol, etc.) and, if a file system media is used, indicate in the Remarks column the exact format(s) supported (i.e., Windows, Mac OS, or Linux file system version, etc.). 
<a name="chartSMF"></a>SMF | [`string`] | Indicate whether or not the device has a mode of operation that can play, import, and/or export any of the Standard MIDI File formats, and, if so, the formats(s) supported: format 0 (single track), format 1 (multitrack), and/or format 2 (multiple independent single-track patterns). If yes, it is also recommended that manufacturers indicate in the Remarks column the means of receiving SMF data (i.e., specific physical format, device interface, or transport protocol, etc.) and, if a file system media is used, indicate in the Remarks column the exact format(s) supported (i.e. Windows, Mac OS, or Linux file system version, etc.).  
<a name="chartXMF"></a>XMF | [`string`] | Indicate whether or not the device has a mode of operation that can play, import, and/or export any of the officially defined XMF File Types: XMF Type 0, XMF Type 1, or Mobile XMF  (XMF Type 2). If the device uses the XMF Meta File Format in a manner that does not conform to any of the XMF File Type specifications, indicate this in the Remarks column. 
<a name="chartSPMIDI"></a>SPMIDI | [RTBoolean Object](#RTBooleanObject) | SP-MIDI compatible? (Yes/No) 

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="chartExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Chart Object Example:

```js
{
  
  
}
```

#### <a name="RTMultiRangeObject"></a>RTMultiRange Object

Object for describing range in the Transmit/Export, Recognize/Import and Remarks columns in the MIDI Implementation Chart. Except this can have multiple ranges for different groups.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RTRangeTransmit"></a>transmit | [[Range Object](#RangeObject)] | The upper and lower limits.
<a name="RTRangeRecognize"></a>recognize | [[Range Object](#RangeObject)] | The upper and lower limits.
<a name="RTRangeRemarks"></a>remarks | `string` | Any further Information.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RTMultiRangeExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### RTMultiRange Object Example:

```js
{
  "transmit": [
	  {
		start:0,
		stop:16,
		group:"A",
	  },
	  {
		start:0,
		stop:16,
		group:"B",
	  }
  ]
}
```

#### <a name="RTRangeObject"></a>RTRange Object

Object for describing range in the Transmit/Export, Recognize/Import and Remarks columns in the MIDI Implementation Chart

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RTRangeTransmit"></a>transmit | [Range Object](#RangeObject) | The upper and lower limts.
<a name="RTRangeRecognize"></a>recognize | [Range Object](#RangeObject) | The upper and lower limts.
<a name="RTRangeRemarks"></a>remarks | `string` | Any further Information.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RTRangeExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### RTRange Object Example:

```js
{
  "transmit": {
	start:0,
	stop:16,
	group:"A",
  },
  "recognize": {
	start:0,
	stop:16,
	group:"A",
  },
  "remarks": "A is uses MIDI output A"
}
```

#### <a name="RangeObject"></a>Range Object

Object for describing range.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RangeStart"></a>start | `integer` | The lower limit.
<a name="RangeStop"></a>stop | `integer` | The upper limit.
<a name="RangeGroup"></a>group | `string` | The Group the range belongs to.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RangeExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Range Object Example:

```js
{
	start:0,
	stop:16,
	group:"A",
}
```

#### <a name="RTBooleanObject"></a>RTBoolean Object

Object for describing the Transmit/Export, Recognize/Import and Remarks columns in the MIDI Implementation Chart

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RTBooleanTransmit"></a>transmit | `boolean` | If not set as true then it assumed false.
<a name="RTBooleanRecognize"></a>recognize | `boolean` | If not set as true then it assumed false.
<a name="RTBooleanRemarks"></a>remarks | `string` | Any further Information.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RTBooleanExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### RTBoolean Object Example:

```js
{
  "transmit": true,
  "recognize": true,
  "remarks": "Only in Song Mode"
}
```

#### <a name="controllersObject"></a>Contollers Object

This describes the controllers available for the MIDI device.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="controllerCC"></a>CC | [CC Object](#ccObject) | A description of the MIDI controller available
<a name="controllerNRPN"></a>NRPN | [NRPN Object](#nrpnObject) | NRPN messages
<a name="controllerMSC"></a>MSC | [MSC Object](#mscObject) | MIDI Show Control messages
<a name="controllerRPN00"></a>RPN00 | [RTBoolean Object](#RTBooleanObject) | RPN 00 (Pitch Bend Sensitivity) (Yes/No)
<a name="controllerRPN01"></a>RPN01 | [RTBoolean Object](#RTBooleanObject) | RPN 01 (Channel Fine Tune) (Yes/No)
<a name="controllerRPN02"></a>RPN02 | [RTBoolean Object](#RTBooleanObject) | RPN 02 (Channel Coarse Tune) (Yes/No)
<a name="controllerRPN03"></a>RPN03 | [RTBoolean Object](#RTBooleanObject) | RPN 03 (Tuning Program Select) (Yes/No) 
<a name="controllerRPN04"></a>RPN04 | [RTBoolean Object](#RTBooleanObject) | RPN 04 (Tuning Bank Select) (Yes/No)
<a name="controllerRPN05"></a>RPN05 | [RTBoolean Object](#RTBooleanObject) | RPN 05 (Modulation Depth Range) (Yes/No) 


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="contollersExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Contollers Object Example:

```js
{
  
  
}
```


#### <a name="sysexObject"></a>Sysex Object

Object for describing the Sysex available to the device.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="sysexSampleDumpStandard"></a>sampleDumpStandard | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to a Sample Dump Sysex.
<a name="sysexDeviceEnquiry"></a>deviceEnquiry | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to this a [Device Enquiry](#deviceEnquiry).
<a name="sysexFileDump"></a>fileDump | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to a File Dump Sysex.
<a name="sysexMidiTuning"></a>midiTuning | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to a MIDI Tuning Sysex.
<a name="sysexMasterVolume"></a>midiVolume | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to a Master Volume Sysex.
<a name="sysexMasterBalance"></a>midiBalance | [RTBoolean Object](#RTBooleanObject) | If True then this device will respond to a Master Balance Sysex.
<a name="sysexExclusiveHeader"></a>exclusiveHeader | [`integer,string`] | This is the header used on all Sysex queries. Strings can be used for Hexidecimal numbers. However Integers are recommended.
<a name="sysexFunctions"></a>functions | [Sysex Functions Object](#sysexFunctionsObject) | This holds the device specific Sysex instruction set
<a name="sysexDefinitions"></a>definitions | [Sysex Definitions Object](#definitionsObject) | Used generally as way to refactor repeated use of sysex data structures.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="sysexExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Object Example:

```js
{
  "deviceEnquiry": {
	"recognize": true
  },
  "masterVolume": {
	"recognize": true
  },
  "exclusiveHeader":["0xF0","0x42","0x3c","0x57"],
  "functions": {
        "0x10":{
          "name": "CURRENT PATTERN DATA DUMP REQUEST"
          ,"recognised":true
        }
   }
  
}
```

#### <a name="sysexFunctionsObject"></a>Sysex Functions Object

Holds the relative paths to the individual Sysex calls as given by the Function Id.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="functionIdSysexFunction"></a>/{functionId} | [Sysex Function Item Object](#sysexFunctionItemObject) | **Required.** An indivdual function Id call. This should be a string of the Hex of the Function Id e.g. `0x4C`
<a name="SysexFunctionExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Functions Object Example

```js
{
	"0x10":{
	  "name": "CURRENT PATTERN DATA DUMP REQUEST"
	  ,"recognised":true
	}
}
```

#### <a name="sysexFunctionItemObject"></a>Sysex Function Item Object

Describes the operations available for a single function.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="sysexFunctionItemName"></a>name | `string` | **Required.** What is the intended purpose of this Function
<a name="sysexFunctionItemRecognized"></a>recognized | `boolean` | Does the device recogize this functions data
<a name="sysexFunctionItemTransmit"></a>transmit | `boolean` | Does the device transmit this functions data
<a name="sysexFunctionItemParts"></a>parts | [[Parts Object](#PartsObject)] | A breakdown of the parts in the sysex data.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="sysexFunctionItemExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Function Item Object Example

```js
{
  "name": "PATTERN WRITE REQUEST"
  ,"recognised":true
  ,"parts":[
	  {
		"byte":1
		,"name":"Destination Program Number"
		,"max":127
	  }
	]
}
```

#### <a name="PartsObject"></a>Parts Object

Describes how to process each byte returned or sent in a Sysex Message.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="partsByte"></a>byte | `integer` | **Required.** The byte after the ExclusiveHeader and the Function Id starting from 0 that you wish to read
<a name="partsLength"></a>length | `integer` | How many bytes to read. If not set it is assumed to only read 1 byte.
<a name="partsName"></a>name | `integer` | **Required.** The name of this value that is being read or sent.
<a name="partsType"></a>type | `string` | The value MUST be one of `"string"`, `"number"`, `"integer"`, `"boolean"` or `"array"`. If not set integer is assumed.
<a name="partsMax"></a>max | `integer` | The maximum number that should be set.
<a name="partsAddValue"></a>addValue | `integer` | When displaying this data and this value to make it more human friendly.
<a name="partsMap"></a>map | [`string`] | Map the value to this array of Strings to make it more human friendly.
<a name="partsItems"></a>items | [Parts Object](#partsObject) | This is required if the type is set to array.
<a name="partsBits"></a>bits | [`string,integer`] | The bits determines which information from the byte(s) thatis required. [See more](#partbitExplanation) below.
<a name="partsSuffix"></a>suffix | `string` | This helps for human readability when display the data.
<a name="partsSchema"></a>schema | [Schema Object](#schemaObject) | The schema defining the type used for the parts parameter. Only use with name, byte and length.

##### Parts Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="operationExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### <a name="partbitExplanation"></a>Parts Bits Explanation
A bits array is made up of the bits in the data collected by the bytes and the length.
A common example is series of on/off switches such as
```js
{
	"byte": 10,
	"name": "Delay BPM Sync Stat",
	"type": "boolean",
	"bits": [1]
}
```

In this example we may recieve byte 10 and it looks like `01111101`. if we break this up into a table:

Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 
---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---
 | 0 | 1 | 1 | 1 | 1 | 1 | 0 | 1
 
 We see that bit 1 is set to O (off).
 
A more complex Example is:
```js
{
	"byte": 0,
	"length": 2,
	"name": "Tempo",
	"type": "number",
	"bits": [14,6,".",3,0]
}
```

In this example we read 2 bytes from byte 0. Which looks like:

Bit | 15 | 14 | 13 | 12 | 11 | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 
---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---
 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 1 
 
The documentation states that the Tempo is built using iiiiiiiii00ffff where iiiiiiiii is the Tempo between 20 and 300 and ffff is a decimal place betwen 0 and 9.
In the bits array we have a start bit (14), followed by an end bit(6), followed by a string ("."), followed by another start bit(3) and another end bit (0).
If we start with the first bit pair (14,6) we get 001111100 which converts to "124". We then add the "." to get "124.". We get the next bit pair (3,0) and get "5". Together this gives us a Tempo of "124.5", and when we see the type is a number this is converted to `124.5`.



##### Parts Object Example

```js
[
  {
	"byte": 0,
	"length": 2,
	"name": "Tempo",
	"type": "number",
	"bits": [14,6,".",3,0]
  },
  {
	"byte": 2,
	"name": "Roll Type",
	"type": "integer",
	"bits": [7,6],
	"addValue": 2,
	"max": 2
  }
}
```



#### <a name="schemaObject"></a>Schema Object

The Schema Object allows the definition of [Part Object](#partObject) as a [JSON Reference](https://tools.ietf.org/html/draft-pbryan-zyp-json-ref-03)

##### Fixed Fields
Field Name | Type | Description
---|:---:|---
<a name="schemaRef"></a>$ref | `string` | As a [JSON Reference](https://tools.ietf.org/html/draft-pbryan-zyp-json-ref-03)

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="schemaExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 


##### Schema Object Examples



```js
{
	"byte": 268,
	"length": 6,
	"name": "Part 1 Parameters ",
	"$ref": "#/sysex/definitions/parameters"
}
```
