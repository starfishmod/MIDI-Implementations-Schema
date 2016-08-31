# MIDI Implementations Schema


#### Version 0.9.1

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
0.9.1 | 2016-08-31 | Tweaking to handle sysex and voice lists

## Definitions

##### <a name="MIDI"></a>MIDI
The specification as defined by the [MIDI Association](https://www.midi.org/)

##### <a name="sysex"></a>Sysex
[System Exclusive messages](https://en.wikipedia.org/wiki/MIDI#System_Exclusive_messages)

##### <a name="cc"></a>Control Change 
Used to send changes on a specific channel. Examples include pan, volume.

##### <a name="nrpn"></a>NRPN 
Non-Registered Parameter Number. Often manufactuers use [NRPN](https://en.wikipedia.org/wiki/NRPN) to send data to and from synthesizers. NRPN usually require between 3 and 4  -  3 byte MIDI messages.

##### <a name="deviceEnquiry"></a>Device Enquiry 
A Universal Sysex message that may return information about the device.

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

The MIS representation of the implementation is made of a single file. Except where the [Chart GM](#chartGM) is set. It is suggested to then pull in that GM spec file if available and merge the banks, controllers and sysex parameters.

By convention, the MIS specification file is named based on the manufacter and model number e.g. `Korg_ES1.json`.

### Data Types

Primitive data types in the MIS Specification are based on the types supported by the [JSON-Schema Draft 4](http://json-schema.org/latest/json-schema-core.html#anchor8). Models are described using the [Schema Object](#schemaObject) which is a subset of JSON Schema Draft 4.

<a name="hexAndDec"></a>Hexidecimal values are often used in written documentation, however as the current JSON standard does not support hexidecimal values these will need to be converted to integers. So the hex number 0x42 (or 42H) will need to be converted to 66.

While this makes it can be unclear for humans to distinguish between the two it will make it easier for the computer to parse. It also expected that the tools used will allow humans to enter and display in either format.

### Schema

#### <a name="misObject"></a>MIS Object

This is the root document object for the implementation specification.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="misMis"></a>MIS | `string` | **Required.** Specifies the MIS Specification version being used. The value MUST be `"0.9.1"`.
<a name="misInfo"></a>info | [Info Object](#infoObject) | **Required.** Provides metadata about the implementation. The metadata can be used by the software if needed.
<a name="misChart"></a>chart | [Chart Object](#chartObject) | **Required.** This provides the basic implementation information.
<a name="misbanks"></a>banks | [Bank Object](#bankObject) | The lists the bank and voice lists for the device.
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
<a name="infoManufacturer"></a>manufacturer | [nameID Object](#nameIDObject) | **Required.** The manufactuer of the device. The ID refers to the ID as listed [MANUFACTURER SYSEX ID NUMBERS](https://www.midi.org/specifications/item/manufacturer-id-numbers) and the [Device Enquiry](#deviceEnquiry). When the Manufactuer id is a 2 byte Id such as `"05 C4"` it should be the integer equivalent i.e. `1476`
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
    "documentVersion": "0.2",
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
<a name="nameIDID"></a>id | `integer` | The id name used as reference

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
<a name="chartMidiChannels"></a>midiChannels | [recognizeTransmit Object](#recognizeTransmitObject) |  Default range 1-16. The range of MIDI channels that the device transmits, exports, responds to, and/or imports. Devices using extended channel systems via multiple cables or input/output ports should list the total number of channels in the appropriate “transmit” or “Recognized” ranges and should use the “name” field in the rangeto indicate the terminology used by the device to identify the extra channels (i.e., “MIDIA, MIDIB”)
<a name="chartNoteNumbers"></a>noteNumbers | [recognizeTransmit Object](#recognizeTransmitObject) | Default range 0-127. The total range of transmitted or recognized notes. 
<a name="chartProgramChange"></a>programChange | [recognizeTransmit Object](#recognizeTransmitObject) | Default range 0-127. Indicate the range of Program Change numbers which are transmitted and/or recognized.
<a name="chartBankSelect"></a>bankSelect | [recognizeTransmit Object](#recognizeTransmitObject) | Indicate whether or not the device correctly responds to Bank Select messages as per the MIDI 1.0 Specification. Devices that respond only to Bank Select MSB (cc #0) but not to the LSB (cc #32) should place a "false" in the “Recognized” column and should indicate this in the “Remarks” column. If the device does correctly respond to Bank Select messages, use the “Remarks” column to indicate what banks or ranges of banks are available in the device. If certain banks are accessible only by MIDI (and not by front panel user control), these should be listed in the “Remarks” column.
<a name="chartMode1"></a>mode1 | [recognizeTransmit Object](#recognizeTransmitObject) | Mode 1: Omni-On, Poly (Yes/No)
<a name="chartMode2"></a>mode2 | [recognizeTransmit Object](#recognizeTransmitObject) | Mode 2: Omni-On, Mono (Yes/No)
<a name="chartMode3"></a>mode3 | [recognizeTransmit Object](#recognizeTransmitObject) | Mode 3: Omni-Off, Poly (Yes/No) 
<a name="chartMode4"></a>mode4 | [recognizeTransmit Object](#recognizeTransmitObject) | Mode 4: Omni-Off, Mono (Yes/No) 
<a name="chartModeMulti"></a>modeMulti | [recognizeTransmit Object](#recognizeTransmitObject) | Multi Mode (Yes/No)
<a name="chartNoteOnVelocity"></a>noteOnVelocity | [recognizeTransmit Object](#recognizeTransmitObject) | Note On Velocity (Yes/No)
<a name="chartNoteOffVelocity"></a>noteOffVelocity | [recognizeTransmit Object](#recognizeTransmitObject) | Note Off Velocity (Yes/No)
<a name="chartChannelAftertouch"></a>channelAftertouch | [recognizeTransmit Object](#recognizeTransmitObject) | Channel AfterTouch (Yes/No)
<a name="chartkeyAftertouch"></a>keyAftertouch | [recognizeTransmit Object](#recognizeTransmitObject) | Poly (Key) Aftertouch (Yes/No) 
<a name="chartPitchBend"></a>pitchBend | [recognizeTransmit Object](#recognizeTransmitObject) | Pitch Bend (Yes/No) 
<a name="chartActiveSense"></a>activeSense | [recognizeTransmit Object](#recognizeTransmitObject) | Active Sensing (Yes/No) 
<a name="chartSytemReset"></a>systemReset | [recognizeTransmit Object](#recognizeTransmitObject) | System Reset (Yes/No) 
<a name="chartTuneRequest"></a>tuneRequest | [recognizeTransmit Object](#recognizeTransmitObject) | Tune Request (Yes/No)  
<a name="chartMidiClock"></a>midiClock | [recognizeTransmit Object](#recognizeTransmitObject) | MIDI Clock (Yes/No)  
<a name="chartSongPos"></a>songPos | [recognizeTransmit Object](#recognizeTransmitObject) | Song Position Pointer (Yes/No)  
<a name="chartSongSelect"></a>songSelect | [recognizeTransmit Object](#recognizeTransmitObject) | Song Select (Yes/No)  
<a name="chartStart"></a>start | [recognizeTransmit Object](#recognizeTransmitObject) | Start (Yes/No)  
<a name="chartContinue"></a>continue | [recognizeTransmit Object](#recognizeTransmitObject) | Continue (Yes/No)   
<a name="chartStop"></a>stop | [recognizeTransmit Object](#recognizeTransmitObject) | Stop (Yes/No)   
<a name="chartMTC"></a>MTC | [recognizeTransmit Object](#recognizeTransmitObject) | MIDI Time Code (Yes/No)  
<a name="chartMMC"></a>MMC | [recognizeTransmit Object](#recognizeTransmitObject) | MIDI Machine Control (Yes/No)  
<a name="chartMSC"></a>MSC | [recognizeTransmit Object](#recognizeTransmitObject) | MIDI Show Control messages 
<a name="chartGM"></a>GM | [`string`] | Indicate whether or not the device has a mode of operation which complies with any of the General MIDI specifications: General MIDI System Level 1 (`GM`), General MIDI System Level 2 (`GM2`) and/or General MIDI Lite (`GMLite`). Vendor specific extentions such as Roland's `GS` and Yamaha `XG` should also be added here. It is assumed that if all standard contollers and sysex for the GM mode are handled by the application and as such do not need to be listed in the MIS.
<a name="chartDLS"></a>DLS | [`string`] | Indicate whether or not the device has a mode of operation that complies with any of the Downloadable Sounds specifications: DLS Level 1 (`DLS`) , DLS Level 2 (`DLS2`, including DLS 2.1 and DLS 2.2), and/or `MobileDLS`.  It is recommended that manufacturers indicate in the Remarks column the means of receiving DLS data (i.e., specific physical format, device interface, or transport protocol, etc.) and, if a file system media is used, indicate in the Remarks column the exact format(s) supported (i.e., Windows, Mac OS, or Linux file system version, etc.). 
<a name="chartSMF"></a>SMF | [`string`] | Indicate whether or not the device has a mode of operation that can play, import, and/or export any of the Standard MIDI File formats, and, if so, the formats(s) supported: format `0` (single track), format `1` (multitrack), and/or format `2` (multiple independent single-track patterns). If yes, it is also recommended that manufacturers indicate in the Remarks column the means of receiving SMF data (i.e., specific physical format, device interface, or transport protocol, etc.) and, if a file system media is used, indicate in the Remarks column the exact format(s) supported (i.e. Windows, Mac OS, or Linux file system version, etc.).  
<a name="chartXMF"></a>XMF | [`string`] | Indicate whether or not the device has a mode of operation that can play, import, and/or export any of the officially defined XMF File Types: XMF Type `0`, XMF Type `1`, or Mobile XMF  (XMF Type `2`). If the device uses the XMF Meta File Format in a manner that does not conform to any of the XMF File Type specifications, indicate this in the Remarks column. 
<a name="chartSPMIDI"></a>SPMIDI | [recognizeTransmit Object](#recognizeTransmitObject) | SP-MIDI compatible? (Yes/No) 

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="chartExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Chart Object Example:

```js
{
	"midiChannels": {
		"transmit":true,
		"recognize": true,
		"remarks": "Memorized"
    },
    "noteNumbers": {
		"transmit":true,
		"recognize": true,
		"remarks": "Specified by Midi Mode for each part"
	}
    ,"programChange": {
		"transmit":true,
		"recognize": true,
		"remarks": "Specified by Midi Mode for each part.  00~3F : A01~64, 40~7F : B01~64"
    }
    ,"mode3": {
		"recognize": true
    },
    "noteOnVelocity": {
		"transmit":true,
		"recognize": true,
		"transmittedRange": [{"start":30,"stop":127}],
		"recognizedRange": [{"start":1,"stop":127}],
		"remarks": "Transmitted Velocity is specified by accent amount"
    },
    "midiClock": {
		"transmit":true,
		"recognize": true
    },
    "activeSense": {
		"transmit":true,
		"recognize": true,
    },
    "songPos": {
		"transmit":true,
		"recognize": true,
		"remarks": "Only in Song Mode"
    },
	"songSelect": {
		"transmit":true,
		"recognize": true,
		"transmittedRange": [{"start":0,"stop":15}],
		"recognizedRange": [{"start":0,"stop":15}],
    },
	"start": {
		"transmit":true,
		"recognize": true
    },
    "continue": {
		"transmit":true,
		"recognize": true
    },
    "stop": {
		"transmit":true,
		"recognize": true
    }
}
```

#### <a name="recognizeTransmitObject"></a>recognizeTransmit Object

Object for describing range in the Transmit/Export, Recognize/Import and Remarks columns in the MIDI Implementation Chart. Except this can have multiple ranges for different groups.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RTRangeTransmitRange"></a>transmitRange | [[Range Object](#RangeObject)] | The upper and lower limits. `transmit` MUST be marked as true
<a name="RTRangeRecognizeRange"></a>recognizeRange | [[Range Object](#RangeObject)] | The upper and lower limits. `recognize` MUST be marked as true
<a name="recognizeTransmitTransmit"></a>transmit | `boolean` | **Required** Does this transmit this feature.
<a name="recognizeTransmitRecognize"></a>recognize | `boolean` | **Required** Does this recognize this feature.
<a name="RTRangeRemarks"></a>name | `string` | Name of the item. Useful when used in NRPN's or in CC information where it is not the standard controller function
<a name="RTRangeRemarks"></a>remarks | `string` | Any further Information.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RTMultiRangeExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### recognizeTransmit Object Example:

```js
{
  "transmit":true,
  "transmitRange": [
	  {
		"start":0,
		"stop":16,
		"name":"MIDI A"
	  },
	  {
		"start":0,
		"stop":16,
		"name":"MIDI B"
	  }
  ]
}
```



#### <a name="RangeObject"></a>Range Object

Object for describing range.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="RangeStart"></a>start | `integer` | **Required.** The lower limit.
<a name="RangeStop"></a>stop | `integer` | **Required.** The upper limit.
<a name="RangeGroup"></a>name | `string` | The name the range belongs to.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="RangeExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Range Object Example:

```js
{
	"start":0,
	"stop":16,
	"name":"MIDI A"
}
```



#### <a name="BanksObject"></a>Banks Object

Object for describing program changes and voicelists.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="bankProgramChange"></a>PC | [numberList Object](#pcListObject) | A description of each program
<a name="bankVoiceBanks"></a>voiceBanks | [voiceBanks Object](#voiceBanksObject) | A description of each voice bank

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="bankExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Banks Object Example:

```js
{
	"PC":{
		"0":"A01"
		,"1":"A02"
		/*...*/
		,"127":"B64"
	},
	"voiceBanks":{
		"0":{
			"name":"GM",
			"voices":{
				"1":"Acoustic Grand Piano"
				/*...*/
			}
		},
		"127":{
			"name":"Drums",
			"drummaps":{
				"1":{
					"name":"GM Drums",
					"voices":{
						"13":"Kick"
						/*...*/
					}
				}
			}
		}
	
	}
}
```

#### <a name="numberListObject"></a>Number List Object

A list of numbers from 0-127 and a string value


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="numberList"></a>{num} | `string` | **Required.** A number between 0-127 and the name of the object as a string
<a name="numberExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 



#### <a name="voiceBanksObject"></a>Voice Banks Object

For each bank list the voices. This refers to using Bank Select MSB (cc #0) when using it from voice banks and the LSB (cc #32) when used in a drum map.


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="voiceList"></a>{MSB0} | [voiceList Object](#voiceListObject) | **Required.** A number between 0-127 using Bank Select MSB (cc #0)
<a name="voiceBankExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 




#### <a name="voiceListObject"></a>voiceList Object

Object for voicelists. A list will either have voices OR drummaps.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="voiceListName"></a>name | `string` | **Required.** A name for this bank
<a name="voiceListVoices"></a>voices | [[numberList Object](#pcListObject)] | A description of each program the num represents LSB (cc #32)
<a name="voiceListDrumMaps"></a>drummaps | [[voiceBank Object](#voiceBankObject)] | A description of each voice in the drum map.

##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="bankExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 







#### <a name="controllersObject"></a>Controllers Object

This describes the controllers available for the MIDI device.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="controllerCC"></a>CC | [CCList Object](#ccListObject) | A description of the MIDI control change parameters available
<a name="controllerNRPN"></a>NRPN | [NRPNList Object](#nrpnListObject) | NRPN messages
<a name="controllerRPN00"></a>RPN00 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 00 (Pitch Bend Sensitivity) (Yes/No)
<a name="controllerRPN01"></a>RPN01 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 01 (Channel Fine Tune) (Yes/No)
<a name="controllerRPN02"></a>RPN02 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 02 (Channel Coarse Tune) (Yes/No)
<a name="controllerRPN03"></a>RPN03 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 03 (Tuning Program Select) (Yes/No) 
<a name="controllerRPN04"></a>RPN04 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 04 (Tuning Bank Select) (Yes/No)
<a name="controllerRPN05"></a>RPN05 | [recognizeTransmit Object](#recognizeTransmitObject) | RPN 05 (Modulation Depth Range) (Yes/No) 


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="controllersExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Controllers Object Example:

```js
{
  "CC":{
	"1":{
		"transmit":true
		,"recognize":true
	}
  }
  ,"NRPN":{
	"5/7":{
		"name":"Part 1 Motion Seq Type"
		,"transmit":true
		,"recognize":true
		,"map":["Off","Smooth","TrigHold"]
		,"MSBOnly":true
	  }
  }
  ,"RPN00":{
	"transmit":true
	,"recognize":true
  }
  
}
```


#### <a name="ccListObject"></a>CC List Object

A list of the MIDI control change parameters available.


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="ccnumcList"></a>{ccNum} | [recognizeTransmit Object](#recognizeTransmitObject) | **Required.** A CC number between 0-127.
<a name="ccListExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### CC List Object Example:

```js
{
  "1":{
	"transmit":true
    ,"recognize":true
  }
  
}
```

#### <a name="nrpnListObject"></a>NRPN List Object

A list of the MIDI NRPN parameters available.


##### Patterned Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="nrpnList"></a>{MSB}/{LSB} | [NRPN Item Object](#nrpnItemObject) | **Required.** This is the integer of the NRPN MSB and LSB values.
<a name="nrpnExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### NRPN Object Example:

```js
{
	"5/7":{
		"name":"Part 1 Motion Seq Type"
		,"transmit":true
		,"recognize":true
		,"map":["Off","Smooth","TrigHold"]
		,"MSBOnly":true
	}
}
```

#### <a name="nrpnItemObject"></a>NRPN Item Object

Describes how the NRPN Data works

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="nrpnItemName"></a>name | `string` | **Required.** The name of this value that is being read or sent.
<a name="nrpnItemTransmit"></a>transmit | `boolean` | **Required.**  Does the device transmit this?
<a name="nrpnItemRecognize"></a>recognize | `boolean` | **Required.**  Does the device recogize this?
<a name="nrpnItemType"></a>type | `string` | The value MUST be one of `"string"`, `"number"`, `"integer"` or `"boolean"`. If not set integer is assumed.
<a name="nrpnItemFormat"></a>format | `string` | this is the format of the data returned. i.e. `note`.
<a name="nrpnItemExpr"></a>expr | `string` | The expression to determine the values from the data. [See more](#partExprExplanation) below.
<a name="nrpnItemRevExpr"></a>revExpr | `string` | The expression to determine the data from the values. [See more](#partExprExplanation) below.
<a name="nrpnItemMin"></a>min | `integer` | The minimum number that should be set. Min is checked before after expr and before revExpr.
<a name="nrpnItemMax"></a>max | `integer` | The maximum number that should be set. Max is checked before after expr and before revExpr.
<a name="nrpnItemMap"></a>map | [`string`] | Map the value to this array of Strings to make it more human friendly.
<a name="nrpnItemSuffix"></a>suffix | `string` | This helps for human readability when display the data.
<a name="nrpnItemMSBOnly"></a>MSBOnly | `boolean` | Only use the MSB of the NRPN. Assume LSB is not sent.



##### Parts Objects 

Field Pattern | Type | Description
---|:---:|---
<a name="operationExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 



##### NRPN Item Object Example:

```js
{
	"name":"Part 1 Motion Seq Type"
	,"transmit":true
	,"recognize":true
	,"map":["Off","Smooth","TrigHold"]
	,"MSBOnly":true
}
```

#### <a name="sysexObject"></a>Sysex Object

Object for describing the Sysex available to the device.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="sysexSampleDumpStandard"></a>sampleDumpStandard | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to a Sample Dump Sysex.
<a name="sysexDeviceEnquiry"></a>deviceEnquiry | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to this a [Device Enquiry](#deviceEnquiry).
<a name="sysexDeviceEnquiryVersion"></a>deviceEnquiryVersion | `string` | The expression on how to build the version number from the 4 version bytes
<a name="sysexFileDump"></a>fileDump | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to a File Dump Sysex.
<a name="sysexMidiTuning"></a>midiTuning | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to a MIDI Tuning Sysex.
<a name="sysexMasterVolume"></a>masterVolume | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to a Master Volume Sysex.
<a name="sysexMasterBalance"></a>masterBalance | [recognizeTransmit Object](#recognizeTransmitObject) | If True then this device will respond to a Master Balance Sysex.
<a name="sysexExclusiveHeader"></a>exclusiveHeader | [`integer`] | This is the header used on all Sysex queries. This will be an array of integers so an exclusive hedader of `"F0 42 3C 57"` will be [240,66,60,87].
<a name="sysexFunctionsList"></a>functions | [Sysex Functions List Object](#sysexFunctionsListObject) | This holds the device specific Sysex instruction set
<a name="sysexDefinitions"></a>definitions | [Sysex Definitions Object](#sysexDefinitionsObject) | Used generally as way to refactor repeated use of sysex data structures.

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
  "exclusiveHeader": [240,66,60,87],
  "functions": {
        "10":{
          "name": "CURRENT PATTERN DATA DUMP REQUEST",
          "transmit":false,
          "recognize":true
        }
   }
  
}
```

#### <a name="sysexFunctionsListObject"></a>Sysex Functions List Object

Holds the relative paths to the individual Sysex calls as given by the Function Id.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="functionIdSysexFunction"></a>{functionId} | [Sysex Function Item Object](#sysexFunctionItemObject) | **Required.** An indivdual function Id call. This should be a integer of the Function id e.g `76` (which is `4C` in hex) 
<a name="SysexFunctionExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Functions Object Example

```js
{
	"10":{
	  "name": "CURRENT PATTERN DATA DUMP REQUEST",
	  "transmit":false,
	  "recognize":true
	}
}
```

#### <a name="sysexFunctionItemObject"></a>Sysex Function Item Object

Describes the operations available for a single function.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="sysexFunctionItemName"></a>name | `string` | **Required.** What is the intended purpose of this Function
<a name="sysexFunctionItemRecognize"></a>recognize | `boolean` | Does the device recogize this functions data
<a name="sysexFunctionItemTransmit"></a>transmit | `boolean` | Does the device transmit this functions data
<a name="sysexFunctionItemParts"></a>parts | [[Parts Object](#PartsObject)] | A breakdown of the parts in the sysex data.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="sysexFunctionItemExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Function Item Object Example

```js
{
  "name": "PATTERN WRITE REQUEST",
  "transmit":false,
  "recognize":true,
  "parts":[
	  {
		"name":"Destination Program Number",
		"max":127
	  }
	]
}
```

#### <a name="PartsObject"></a>Parts Object

Each part should be read sequentially after the header and the function id. Each part is assumed to be a length of one byte unless a length or length expression field is used. If reserved or empty parts of the sysex data are required place a partObject that is empty to represent 1 byte or a length field to represent a number of empty bytes.

Note that because sysex data only uses lowest 7 bits (bits 6-0) of the byte (bit 7 is 0). When reading in 2 bytes it will read the value in as a 14bit value NOT a 16bit value. 3 bytes is a 21 bit value etc.

Individual bits can also be read and set using the bitPart object.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="partsName"></a>name | `string` | The name of this value that is being read or sent.
<a name="partsLength"></a>length | `integer` | How many bytes to read. If not set it is assumed to only read 1 byte. 
<a name="partsLengthExpr"></a>lengthExpr | `string` | How many bytes to read, but use an expression to deteremine the length. This is useful if one part of the sysex tells you the length of a sample for example.
<a name="partsIfExpr"></a>ifExpr | `string` | Use an expression to determin if this should be read. If false do not move to the next byte
<a name="partsSetVariable"></a>setVariable | `string` | Set the value read to a global variable that can be used in expressions.  Variables MUST match the regexp /[a-z][a-z0-9]*/i
<a name="partsType"></a>type | `string` | The value MUST be one of `"string"`, `"number"`, `"integer"` or `"boolean"`. If not set integer is assumed.
<a name="partsFormat"></a>format | `string` | this is the format of the data returned. i.e. `note`.
<a name="partsRepeat"></a>repeat | `integer` | How many times are we going to read this byte and length. This is useful where the docmentation ask to read x amount of the same param
<a name="partsRepeatTitles"></a>repeatTitles | [`string`] | The title of each repeat. The length of this array MUST match the repeat value.
<a name="partsSuffix"></a>suffix | `string` | This helps for human readability when display the data. e.g. '%'
<a name="partsoffset"></a>offset | `string` | This will adjust the value for visual display. 
<a name="partsExpr"></a>expr | `string` | The expression to determine the values from the data. This MUST not be used with addValue. An expression MUST have a revExpr [See more](#partExprExplanation) below.
<a name="partsRevExpr"></a>revExpr | `string` | The expression to determine the data from the values. [See more](#partExprExplanation) below.
<a name="partsMin"></a>min | `integer` | The minimum number that should be set. Min is checked before after expr and before revExpr.
<a name="partsMax"></a>max | `integer` | The maximum number that should be set. Max is checked before after expr and before revExpr.
<a name="partsMap"></a>map | [`string`] | Map the value to this array of Strings to make it more human friendly.
<a name="partsSchema"></a>schema | [Schema Object](#schemaObject) | The schema defining the type used for the parts parameter. Only use with name and length.
<a name="partsParts"></a>parts | [Parts Object](#partsObject) | Break down the value grabbed by the byte and length into easier components. This is usefult if you are also repeating blocks of data. Only use with name, length, repeat and repeatTitles.
<a name="partsBitParts"></a>bitParts | [bitParts Object](#bitpartsObject) | Break down the value into indiviual bit ranges.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="operationExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Part Object Example

```js
{
	"name": "Swing",
	"type": "integer",
	"suffix": "%",
	"offset":50,
	"max": 50,
	"min": 75
}
```

##### Part Object Example - Mapping

```js
{
	"name": "Effect Type",
	"type": "integer",
	"max": 10,
	"map": [
		"Flanger/Chorus",
		"Phaser",
		"Ring Modulation",
		"Pitch Shifter",
		"Compressor",
		"Distortion",
		"Decimator",
		"Isolator",
		"Resonance Filter",
		"Wah"
	]
}
```

##### Part Object Example - Repeat

```js
{
	"length":128,
	"repeat":10,
	"Name": "Parts",
	"repeatTitles":["Part 1","Part 2","Part 3","Part 4","Part 5","Part 6A","Part 6B","Part 7A","Part 7B","Slice"],
	"parts":[
		{
			"name": "Parameters",
			"length":6,
			"schema": {
				"$ref": "#/sysex/definitions/parameters"
			}
		},
		{
			"length": 8,
			"name": "StepSequence Data ",
			"schema": {
				"$ref": "#/sysex/definitions/stepSequence"
			}
		},
		{
			"length": 114,
			"name": "MotionSequence Data ",
			"schema": {
				"$ref": "#/sysex/definitions/motionSequence"
			}
		}
	]
	
}
```


#### <a name="bitPartsObject"></a>bitParts Object

A bit part object allows use to access individual bits in the data. Often these will include on/off switches (boolean) or option data.

##### Fixed Fields

Field Name | Type | Description
---|:---:|---
<a name="bitPartsName"></a>name | `string` | The name of this value that is being read or sent.
<a name="bitPartsBit"></a>bit | `integer` | **Required** Start reading bits from. 
<a name="bitPartsLength"></a>length | `integer` | **Required** How many bits to read. 
<a name="bitPartsIfExpr"></a>ifExpr | `string` | Use an expression to determin if this should be read.
<a name="bitPartsSetVariable"></a>setVariable | `string` | Set the value read to a global variable that can be used in expressions. Variables MUST match the regexp /[a-z][a-z0-9]*/i
<a name="bitPartsType"></a>type | `string` | The value MUST be one of `"string"`, `"number"`, `"integer"` or `"boolean"`. If not set integer is assumed.
<a name="bitPartsFormat"></a>format | `string` | this is the format of the data returned. i.e. `note`.
<a name="bitPartsRepeat"></a>repeat | `integer` | How many times are we going to read this byte and length. This is useful where the docmentation ask to read x amount of the same param
<a name="bitPartsRepeatTitles"></a>repeatTitles | [`string`] | The title of each repeat. The length of this array MUST match the repeat value.
<a name="bitPartsSuffix"></a>suffix | `string` | This helps for human readability when display the data. e.g. '%'
<a name="bitPartsoffset"></a>offset | `string` | This will adjust the value for visual display. 
<a name="bitPartsExpr"></a>expr | `string` | The expression to determine the values from the data. This MUST not be used with addValue. An expression MUST have a revExpr [See more](#partExprExplanation) below.
<a name="bitPartsRevExpr"></a>revExpr | `string` | The expression to determine the data from the values. [See more](#partExprExplanation) below.
<a name="bitPartsMin"></a>min | `integer` | The minimum number that should be set. Min is checked before after expr and before revExpr.
<a name="bitPartsMax"></a>max | `integer` | The maximum number that should be set. Max is checked before after expr and before revExpr.
<a name="bitPartsMap"></a>map | [`string`] | Map the value to this array of Strings to make it more human friendly.

##### Patterned Fields 

Field Pattern | Type | Description
---|:---:|---
<a name="operationExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### bitPart Object Example

```js
{
	"bitParts":[
		{
			"bit": 6,
			"length":2,
			"name": "Roll Type",
			"type": "integer",
			"offset": 1,
			"max": 3,
			"min": 1
		  },
		  {
			"bit": 4,
			"length":2,
			"name": "Scale/Beat",
			"type": "integer",
			"map": [
			  "16th",
			  "32nd",
			  "tri",
			  "tr2"
			]
		  },
		  {
			"bit": 1,
			"length":2,
			"name": "Pattern Length",
			"type": "integer",
			"offset":1,
			"max": 4,
			"min": 1
		  }
	]
}
```

#### <a name="sysexDefinitionsObject"></a>Sysex Definitions Object

Used for refactoring Sysex data.

##### Patterned Fields

Field Pattern | Type | Description
---|:---:|---
<a name="sysexdefinition"></a>{definition} | [Parts Object](#partsObject) | **Required.** A string of the definition
<a name="SysexFunctionExtensions"></a>^x- | Any | Allows extensions to the MIS Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object. 

##### Sysex Functions Object Example

```js
{
    "motionSequence": [
        {
          "repeat": 64,
          "name": "Step",
          "type": "boolean"
        },
        {
          "repeat": 64,
          "name": "Step Value",
          "type": "integer"
        }
    ]
    
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
	"length": 6,
	"name": "Part 1 Parameters ",
	"schema": {
		"$ref": "#/sysex/definitions/parameters"
	}
}
```

##### <a name="byteLengthExplanation"></a>Byte Length Explanation
In an effort to make reading and writing bit data into bytes without effecting other bits the MIS schema defines a way to reach indivdual bits and the lengths needed.

A common example is series of on/off switches such as.

```js
{
	"bitParts":[
		{
			"bit": 1,
			"length": 1,
			"name": "Delay BPM Sync Stat",
			"type": "boolean"
		  },
		  {
			"bit": 0,
			"length": 1,
			"name": "Delay MotionSEQ Stat",
			"type": "boolean"
		  }
	]
}
```

In this example we may a recieve byte and it looks like `01111101`. if we break this up into a table:

Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 
---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---
 | 0 | 1 | 1 | 1 | 1 | 1 | 0 | 1
 
We see that bit 1 (Delay BPM Sync Stat) is set to 0 (off). So starting from bit 1 and getting the single bit as determined by the length of 1 we get the value of zero. It would also be easy to change the value as we know the value is only 1 bit in length and it is placed into bit 1.



##### <a name="partExprExplanation"></a>Parts Expression Explanation
It is hoped that any MIS library or toolset is able to use the Expression set as defined to make the information in stored in Sysex not only readable but also have the ability to be manipulated and changed easily. With this in mind each part has the use of an `expr` and `revExpr` fields to read and modify data.

Note that `$` represents the value of the expression, and `@` represent the current value in the byte(s) returned. Variables set by setVariable will also be available.

Expression are written in normal expression format with bitwise and basic math function:
* 5 + 4
* (5 + @) *2
* floor($) << 7 & 0b1111

A common example is series of on/off switches such as - although this would be handled by Byte Length bit parameters normally.

```js
{
  "byte":10,
  "name": "Delay BPM Sync Stat",
  "type": "boolean",
  "expr":"(0b10 & @) >> 1",
  "revExpr":"(@ & 0b1111101 | ($ <<1))"
}
```

In this example we may recieve byte 10 and it looks like `01111101`. if we break this up into a table:

Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 
---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---
 | 0 | 1 | 1 | 1 | 1 | 1 | 0 | 1
 
We see that bit 1 is set to 0 (off). So getting `b(1)` we get a 0 which equates to boolean false.
 
A more complex Example is:
```js
{
  "byte": 0,
  "length":2,
  "name": "Tempo",
  "type": "float",
  "expr":"(@ >> 5) + (@ & 0b1111)/10",
  "revExpr":"floor($) << 5 + ($ * 10  % 10 )"
}
```

In this example we read 2 bytes from byte 0. Which looks like:

Bit  | * | 13 | 12 | 11 | 10 | 9 | 8 | 7 | * | 6 | 5 | 4 | 3 | 2 | 1 | 0 
---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---
 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 1 
 
Note that in sysex the bit 7 is always 0. So returning 2 bytes actually gives us a 14bit value not a 16bit value.
 
The documentation states that the Tempo is built using iiiiiiiii00ffff where iiiiiiiii is the Tempo between 20 and 300 and ffff is a decimal place betwen 0 and 9.
The expr uses standard bitwise operators to convert the value into 124.5.

The revExpr tells s how to turn this information back. If we had a tempo of 132.2 it gets the floor of val of 132 and then puts that into bits 13 to 7. It then gets the 0.2 turns it into 2 puts that into bits 3 to 0.

It will then be up to the application to split this 14bit value into 2 8bit values.

The expressions parser MUST handle the following
* functions:
 * floor
 * ceil
* operands - please follow the order of operations guidelines at https://en.wikipedia.org/wiki/Order_of_operations#Programming_language
* variables - 
 * $ - the value to be put into the byte(s). Only used in revExpr
 * @ - the value currently in the byte(s)
 * variables set by setVariable




