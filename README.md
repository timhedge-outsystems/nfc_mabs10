# PhoneGap NFC Plugin

The NFC plugin allows you to read NFC tags.

Use to

- read data from NFC tags
- receive data from NFC devices

This plugin uses NDEF (NFC Data Exchange Format) for maximum compatibilty between NFC devices, tag types, and operating systems.

## Supported Platforms

- Android
- [iOS 11](#ios-notes)

## Contents

- [Installing](#installing)
- [NFC](#nfc)
- [NDEF](#ndef)
  - [NdefMessage](#ndefmessage)
  - [NdefRecord](#ndefrecord)
- [Events](#events)
- [Platform Differences](#platform-differences)
- [Launching Application when Scanning a Tag](#launching-your-android-application-when-scanning-a-tag)
- [Testing](#testing)
- [Host Card Emulation (HCE)](#hce)
- [Book](#book)
- [License](#license)

# Installing

### Cordova

    $ cordova plugin add nfc_mabs10

### PhoneGap

    $ phonegap plugin add nfc_mabs10

### PhoneGap Build

Edit config.xml to install the plugin for [PhoneGap Build](http://build.phonegap.com).

    <gap:plugin name="nfc_mabs10" source="npm" />

See [Getting Started](https://github.com/chariotsolutions/nfc_mabs10/blob/master/doc/GettingStartedCLI.md) for more details.

## iOS Notes

Reading NFC NDEF tags is supported on iPhone 7 and iPhone 7 Plus running iOS 11. To enable your app to detect NFC tags, the plugin adds the Near Field Communication Tag Reading capability in your Xcode project. You must build your application with XCode 9. See the [Apple Documentation](http://help.apple.com/xcode/mac/current/#/dev88ff319e7) for more info.

Use [nfc.addNdefListener](#nfcaddndeflistener) to read NDEF NFC tags with iOS. Unfortunately, iOS also requires you to begin a session before scanning NFC tag. The JavaScript API contains two new iOS specific functions [nfc.beginSession](#nfcbeginsession) and [nfc.invalidateSession](#nfcinvalidatesession).

You must call [nfc.beginSession](#nfcbeginsession) before every scan.

The initial iOS version plugin does not support scanning multiple tags (invalidateAfterFirstRead:FALSE) or setting the alertMessage. If you have use cases or suggestions on the best way to support multi-read or alert messages, open a ticket for discussion.

# NFC

> The nfc object provides access to the device's NFC sensor.

## Methods

- [nfc.addNdefListener](#nfcaddndeflistener)
- [nfc.removeNdefListener](#nfcremovendeflistener)
- [nfc.addTagDiscoveredListener](#nfcaddtagdiscoveredlistener)
- [nfc.removeTagDiscoveredListener](#nfcremovetagdiscoveredlistener)
- [nfc.addMimeTypeListener](#nfcaddmimetypelistener)
- [nfc.removeMimeTypeListener](#nfcremovemimetypelistener)
- [nfc.addNdefFormatableListener](#nfcaddndefformatablelistener)
- [nfc.write](#nfcwrite)
- [nfc.makeReadOnly](#nfcmakereadonly)
- [nfc.share](#nfcshare)
- [nfc.unshare](#nfcunshare)
- [nfc.erase](#nfcerase)
- [nfc.handover](#nfchandover)
- [nfc.stopHandover](#nfcstophandover)
- [nfc.enabled](#nfcenabled)
- [nfc.showSettings](#nfcshowsettings)
- [nfc.beginSession](#nfcbeginsession)
- [nfc.invalidateSession](#nfcinvalidatesession)

## nfc.addNdefListener

Registers an event listener for any NDEF tag.

    nfc.addNdefListener(callback, [onSuccess], [onFailure]);

### Parameters

- **callback**: The callback that is called when an NDEF tag is read.
- **onSuccess**: (Optional) The callback that is called when the listener is added.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.addNdefListener` registers the callback for ndef events.

A ndef event is fired when a NDEF tag is read.

For BlackBerry 10, you must configure the type of tags your application will read with an [invoke-target in config.xml](#blackberry-10-invoke-target).

On Android registered [mimeTypeListeners](#nfcaddmimetypelistener) takes precedence over this more generic NDEF listener.

On iOS you must call [beingSession](#nfcbeginsession) before scanning a tag.

### Supported Platforms

- Android
- iOS

## nfc.removeNdefListener

Removes the previously registered event listener for NDEF tags added via `nfc.addNdefListener`.

    nfc.removeNdefListener(callback, [onSuccess], [onFailure]);

### Parameters

- **callback**: The previously registered callback.
- **onSuccess**: (Optional) The callback that is called when the listener is successfully removed.
- **onFailure**: (Optional) The callback that is called if there was an error during removal.

### Supported Platforms

- Android
- iOS

## nfc.addTagDiscoveredListener

Registers an event listener for tags matching any tag type.

    nfc.addTagDiscoveredListener(callback, [onSuccess], [onFailure]);

### Parameters

- **callback**: The callback that is called when a tag is detected.
- **onSuccess**: (Optional) The callback that is called when the listener is added.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.addTagDiscoveredListener` registers the callback for tag events.

This event occurs when any tag is detected by the phone.

### Supported Platforms

- Android

## nfc.removeTagDiscoveredListener

Removes the previously registered event listener added via `nfc.addTagDiscoveredListener`.

    nfc.removeTagDiscoveredListener(callback, [onSuccess], [onFailure]);

### Parameters

- **callback**: The previously registered callback.
- **onSuccess**: (Optional) The callback that is called when the listener is successfully removed.
- **onFailure**: (Optional) The callback that is called if there was an error during removal.

### Supported Platforms

- Android

## nfc.addMimeTypeListener

Registers an event listener for NDEF tags matching a specified MIME type.

    nfc.addMimeTypeListener(mimeType, callback, [onSuccess], [onFailure]);

### Parameters

- **mimeType**: The MIME type to filter for messages.
- **callback**: The callback that is called when an NDEF tag matching the MIME type is read.
- **onSuccess**: (Optional) The callback that is called when the listener is added.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.addMimeTypeListener` registers the callback for ndef-mime events.

A ndef-mime event occurs when a `Ndef.TNF_MIME_MEDIA` tag is read and matches the specified MIME type.

This function can be called multiple times to register different MIME types. You should use the _same_ handler for all MIME messages.

    nfc.addMimeTypeListener("text/json", *onNfc*, success, failure);
    nfc.addMimeTypeListener("text/demo", *onNfc*, success, failure);

On Android, MIME types for filtering should always be lower case. (See [IntentFilter.addDataType()](<http://developer.android.com/reference/android/content/IntentFilter.html#addDataType(java.lang.String)>))

### Supported Platforms

- Android

## nfc.removeMimeTypeListener

Removes the previously registered event listener added via `nfc.addMimeTypeListener`.

    nfc.removeMimeTypeListener(mimeType, callback, [onSuccess], [onFailure]);

### Parameters

- **mimeType**: The MIME type to filter for messages.
- **callback**: The previously registered callback.
- **onSuccess**: (Optional) The callback that is called when the listener is successfully removed.
- **onFailure**: (Optional) The callback that is called if there was an error during removal.

### Supported Platforms

- Android

## nfc.addNdefFormatableListener

Registers an event listener for formatable NDEF tags.

    nfc.addNdefFormatableListener(callback, [onSuccess], [onFailure]);

### Parameters

- **callback**: The callback that is called when NDEF formatable tag is read.
- **onSuccess**: (Optional) The callback that is called when the listener is added.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.addNdefFormatableListener` registers the callback for ndef-formatable events.

A ndef-formatable event occurs when a tag is read that can be NDEF formatted. This is not fired for tags that are already formatted as NDEF. The ndef-formatable event will not contain an NdefMessage.

### Supported Platforms

- Android

## nfc.makeReadOnly

Makes a NFC tag read only. **Warning this is permanent.**

    nfc.makeReadOnly([onSuccess], [onFailure]);

### Parameters

- **onSuccess**: (Optional) The callback that is called when the tag is locked.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.makeReadOnly` make a NFC tag read only. **Warning this is permanent** and can not be undone.

On **Android** this method _must_ be called from within an NDEF Event Handler.

Example usage

    onNfc: function(nfcEvent) {

        var record = [
            ndef.textRecord("hello, world")
        ];

        var failure = function(reason) {
            alert("ERROR: " + reason);
        };

        var lockSuccess = function() {
            alert("Tag is now read only.");
        };

        var lock = function() {
            nfc.makeReadOnly(lockSuccess, failure);
        };

        nfc.write(record, lock, failure);

    },

### Supported Platforms

- Android

## nfc.share

Shares an NDEF Message via peer-to-peer.

A NDEF Message is an array of one or more NDEF Records

    var message = [
        ndef.textRecord("hello, world")
    ];

    nfc.share(message, [onSuccess], [onFailure]);

### Parameters

- **ndefMessage**: An array of NDEF Records.
- **onSuccess**: (Optional) The callback that is called when the message is pushed.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.share` writes an NdefMessage via peer-to-peer. This should appear as an NFC tag to another device.

### Supported Platforms

- Android

### Platform differences

    Android - shares message until unshare is called
    Blackberry 10 - shares the message one time or until unshare is called
    Windows Phone 8 - must be called from within a NFC event handler like nfc.write

## nfc.unshare

Stop sharing NDEF data via peer-to-peer.

    nfc.unshare([onSuccess], [onFailure]);

### Parameters

- **onSuccess**: (Optional) The callback that is called when sharing stops.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.unshare` stops sharing data via peer-to-peer.

### Supported Platforms

- Android

## nfc.erase

Erase a NDEF tag

    nfc.erase([onSuccess], [onFailure]);

### Parameters

- **onSuccess**: (Optional) The callback that is called when sharing stops.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.erase` erases a tag by writing an empty message. Will format unformatted tags before writing.

This method _must_ be called from within an NDEF Event Handler.

### Supported Platforms

- Android

## nfc.handover

Send a file to another device via NFC handover.

    var uri = "content://media/external/audio/media/175";
    nfc.handover(uri, [onSuccess], [onFailure]);


    var uris = [
        "content://media/external/audio/media/175",
        "content://media/external/audio/media/176",
        "content://media/external/audio/media/348"
    ];
    nfc.handover(uris, [onSuccess], [onFailure]);

### Parameters

- **uri**: A URI as a String, or an _array_ of URIs.
- **onSuccess**: (Optional) The callback that is called when the message is pushed.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.handover` shares files to a NFC peer using handover. Files are sent by specifying a file:// or context:// URI or a list of URIs. The file transfer is initiated with NFC but the transfer is completed with over Bluetooth or WiFi which is handled by a NFC handover request. The Android code is responsible for building the handover NFC Message.

This is Android only, but it should be possible to add implementations for other platforms.

### Supported Platforms

- Android

## nfc.stopHandover

Stop sharing NDEF data via NFC handover.

    nfc.stopHandover([onSuccess], [onFailure]);

### Parameters

- **onSuccess**: (Optional) The callback that is called when sharing stops.
- **onFailure**: (Optional) The callback that is called if there was an error.

### Description

Function `nfc.stopHandover` stops sharing data via peer-to-peer.

### Supported Platforms

- Android

## nfc.showSettings

Show the NFC settings on the device.

    nfc.showSettings(success, failure);

### Description

Function `showSettings` opens the NFC settings for the operating system.

### Parameters

- **success**: Success callback function [optional]
- **failure**: Error callback function, invoked when error occurs. [optional]

### Quick Example

    nfc.showSettings();

### Supported Platforms

- Android

## nfc.enabled

Check if NFC is available and enabled on this device.

nfc.enabled(onSuccess, onFailure);

### Parameters

- **onSuccess**: The callback that is called when NFC is enabled.
- **onFailure**: The callback that is called when NFC is disabled or missing.

### Description

Function `nfc.enabled` explicitly checks to see if the phone has NFC and if NFC is enabled. If
everything is OK, the success callback is called. If there is a problem, the failure callback
will be called with a reason code.

The reason will be **NO_NFC** if the device doesn't support NFC and **NFC_DISABLED** if the user has disabled NFC.

Note: that on Android the NFC status is checked before every API call **NO_NFC** or **NFC_DISABLED** can be returned in **any** failure function.

Windows will return **NO_NFC_OR_NFC_DISABLED** when NFC is not present or disabled. If the user disabled NFC after the application started, Windows may return **NFC_DISABLED**. Windows checks the NFC status before most API calls, but there are some cases when the NFC state can not be determined.

### Supported Platforms

- Android

## nfc.beginSession

iOS requires you to begin a session before scanning a NFC tag.

    nfc.beginSession(success, failure);

### Description

Function `beginSession` starts the [NFCNDEFReaderSession](https://developer.apple.com/documentation/corenfc/nfcndefreadersession) allowing iOS to scan NFC tags.

### Parameters

- **success**: Success callback function called when the session begins [optional]
- **failure**: Error callback function, invoked when error occurs. [optional]

### Quick Example

    nfc.beginSession();

### Supported Platforms

- iOS

## nfc.invalidateSession

Invalidate the NFC session.

    nfc.invalidateSession(success, failure);

### Description

Function `invalidateSession` stops the [NFCNDEFReaderSession](https://developer.apple.com/documentation/corenfc/nfcndefreadersession) returning control to your app.

### Parameters

- **success**: Success callback function called when the session in invalidated [optional]
- **failure**: Error callback function, invoked when error occurs. [optional]

### Quick Example

    nfc.invalidateSession();

### Supported Platforms

- iOS

# NDEF

> The `ndef` object provides NDEF constants, functions for creating NdefRecords, and functions for converting data.
> See [android.nfc.NdefRecord](http://developer.android.com/reference/android/nfc/NdefRecord.html) for documentation about constants

## NdefMessage

Represents an NDEF (NFC Data Exchange Format) data message that contains one or more NdefRecords.
This plugin uses an array of NdefRecords to represent an NdefMessage.

## NdefRecord

Represents a logical (unchunked) NDEF (NFC Data Exchange Format) record.

### Properties

- **tnf**: 3-bit TNF (Type Name Format) - use one of the TNF\_\* constants
- **type**: byte array, containing zero to 255 bytes, must not be null
- **id**: byte array, containing zero to 255 bytes, must not be null
- **payload**: byte array, containing zero to (2 \*\* 32 - 1) bytes, must not be null

The `ndef` object has a function for creating NdefRecords

    var type = "text/pg",
        id = [],
        payload = nfc.stringToBytes("Hello World"),
        record = ndef.record(ndef.TNF_MIME_MEDIA, type, id, payload);

There are also helper functions for some types of records

Create a URI record

    var record = ndef.uriRecord("http://chariotsolutions.com");

Create a plain text record

    var record = ndef.textRecord("Plain text message");

Create a mime type record

    var mimeType = "text/pg",
        payload = "Hello Phongap",
        record = ndef.mimeMediaRecord(mimeType, nfc.stringToBytes(payload));

Create an Empty record

    var record = ndef.emptyRecord();

Create an Android Application Record (AAR)

    var record = ndef.androidApplicationRecord('com.example');

See `ndef.record`, `ndef.textRecord`, `ndef.mimeMediaRecord`, and `ndef.uriRecord`.

The Ndef object has functions to convert some data types to and from byte arrays.

See the [nfc_mabs10.js](https://github.com/chariotsolutions/nfc_mabs10/blob/master/www/nfc_mabs10.js) source for more documentation.

# Events

Events are fired when NFC tags are read. Listeners are added by registering callback functions with the `nfc` object. For example ` nfc.addNdefListener(myNfcListener, win, fail);`

## NfcEvent

### Properties

- **type**: event type
- **tag**: Ndef tag

### Types

- tag
- ndef-mime
- ndef
- ndef-formatable

The tag contents are platform dependent.

`id` and `techTypes` may be included when scanning a tag on Android. `serialNumber` may be included on BlackBerry 7.

`id` and `serialNumber` are different names for the same value. `id` is typically displayed as a hex string `nfc.bytesToHexString(tag.id)`.

Assuming the following NDEF message is written to a tag, it will produce the following events when read.

    var ndefMessage = [
        ndef.createMimeRecord('text/pg', 'Hello PhoneGap')
    ];

#### Sample Event on Android

    {
        type: 'ndef',
        tag: {
            "isWritable": true,
            "id": [4, 96, 117, 74, -17, 34, -128],
            "techTypes": ["android.nfc.tech.IsoDep", "android.nfc.tech.NfcA", "android.nfc.tech.Ndef"],
            "type": "NFC Forum Type 4",
            "canMakeReadOnly": false,
            "maxSize": 2046,
            "ndefMessage": [{
                "id": [],
                "type": [116, 101, 120, 116, 47, 112, 103],
                "payload": [72, 101, 108, 108, 111, 32, 80, 104, 111, 110, 101, 71, 97, 112],
                "tnf": 2
            }]
        }
    }

## Getting Details about Events

The raw contents of the scanned tags are written to the log before the event is fired. Use `adb logcat` on Android and Event Log (hold alt + lglg) on BlackBerry.

You can also log the tag contents in your event handlers. `console.log(JSON.stringify(nfcEvent.tag))` Note that you want to stringify the tag not the event to avoid a circular reference.

# Platform Differences

## Non-NDEF Tags

Only Android can read data from non-NDEF NFC tags. Newer Windows Phones with NXP PN427 chipset can read non-NDEF tags, but can not get any tag meta data.

## Mifare Classic Tags

Nwer Android phones will not read Mifare Classic tags. Mifare Ultralight tags will work since they are NFC Forum Type 2 tags.

## Multiple Listeners

Multiple listeners can be registered in JavaScript. e.g. addNdefListener, addTagDiscoveredListener, addMimeTypeListener.

On Android, only the most specific event will fire. If a Mime Media Tag is scanned, only the addMimeTypeListener callback is called and not the callback defined in addNdefListener. You can use the same event handler for multiple listeners.

For Windows, this plugin mimics the Android behavior. If an ndef event is fired, a tag event will not be fired. You should receive one event per tag.

## addTagDiscoveredListener

On Android, addTagDiscoveredListener scans non-NDEF tags and NDEF tags. The tag event does NOT contain an ndefMessage even if there are NDEF messages on the tag. Use addNdefListener or addMimeTypeListener to get the NDEF information.

### Non-NDEF tag scanned with addTagDiscoveredListener on _Android_

    {
        type: 'tag',
        tag: {
            "id": [-81, 105, -4, 64],
            "techTypes": ["android.nfc.tech.MifareClassic", "android.nfc.tech.NfcA", "android.nfc.tech.NdefFormatable"]
        }
    }

### NDEF tag scanned with addTagDiscoveredListener on _Android_

    {
        type: 'tag',
        tag: {
            "id": [4, 96, 117, 74, -17, 34, -128],
            "techTypes": ["android.nfc.tech.IsoDep", "android.nfc.tech.NfcA", "android.nfc.tech.Ndef"]
        }
    }

# Launching your Android Application when Scanning a Tag

On Android, intents can be used to launch your application when a NFC tag is read. This is optional and configured in AndroidManifest.xml.

    <intent-filter>
      <action android:name="android.nfc.action.NDEF_DISCOVERED" />
      <data android:mimeType="text/pg" />
      <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>

Note: `data android:mimeType="text/pg"` should match the data type you specified in JavaScript

We have found it necessary to add `android:noHistory="true"` to the activity element so that scanning a tag launches the application after the user has pressed the home button.

See the Android documentation for more information about [filtering for NFC intents](http://developer.android.com/guide/topics/connectivity/nfc/nfc.html#ndef-disc).

# Testing

Tests require the [Cordova Plugin Test Framework](https://github.com/apache/cordova-plugin-test-framework)

Create a new project

    git clone https://github.com/chariotsolutions/nfc_mabs10
    cordova create nfc-test com.example.nfc.test NfcTest
    cd nfc-test
    cordova platform add android
    cordova plugin add ../nfc_mabs10
    cordova plugin add ../nfc_mabs10/tests
    cordova plugin add http://git-wip-us.apache.org/repos/asf/cordova-plugin-test-framework.git

Change the start page in `config.xml`

    <content src="cdvtests/index.html" />

Run the app on your phone

    cordova run

# HCE

For Host Card Emulation (HCE), try the [Cordova HCE Plugin](https://github.com/don/cordova-plugin-hce).

# Book

Need more info? Check out my book <a href="http://www.tkqlhce.com/click-7835726-11260198-1430755877000?url=http%3A%2F%2Fshop.oreilly.com%2Fproduct%2F0636920021193.do%3Fcmp%3Daf-prog-books-videos-product_cj_9781449372064_%2525zp&cjsku=0636920021193" target="_top">
Beginning NFC: Near Field Communication with Arduino, Android, and PhoneGap</a><img src="http://www.lduhtrp.net/image-7835726-11260198-1430755877000" width="1" height="1" border="0"/>

<a href="http://www.kqzyfj.com/click-7835726-11260198-1430755877000?url=http%3A%2F%2Fshop.oreilly.com%2Fproduct%2F0636920021193.do%3Fcmp%3Daf-prog-books-videos-product_cj_9781449372064_%2525zp&cjsku=0636920021193" target="_top"><img src="http://akamaicovers.oreilly.com/images/0636920021193/cat.gif" border="0" alt="Beginning NFC"/></a><img src="http://www.ftjcfx.com/image-7835726-11260198-1430755877000" width="1" height="1" border="0"/>

# License

The MIT License

Copyright (c) 2011-2017 Chariot Solutions

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
