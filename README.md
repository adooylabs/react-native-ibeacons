[![npm](https://img.shields.io/npm/v/npm.svg)](https://github.com/MacKentoch/react-native-ibeacons)

# react-native-ibeacons

![logo](./images/RN-iBeacon.png)

`react-native-ibeacons`: add beacon technology in your React Native application for both iOS and Android.

This repository is born to keep alive and up to date these 2 original awesome:
- [ibeacon for android](https://github.com/mmazzarolo/react-native-beacons-android)
- [ibeacon for iOS](https://github.com/frostney/react-native-ibeacon)

If you want to know more about just have a look at [my medium article](https://medium.com/@erwan.datin/mmazzarolohow-to-play-with-ibeacons-in-a-react-native-application-5cef754b2edc#.e2bvgplvy).

## Install (iOS and Android)

> Ensure to have NodeJS >= v6.x.
>
> You must run on **real devices** (*don't forget to active Bluetooth when running*).

**Mobile Version compatibility:**

> - **iOS** `minimum version`
>   -  8.0
> - **Android** `minimum version`
>   - 21 (*alias LOLLIPOP*)


### 1. get modules
*via npm:*
```javascript:
npm install react-native-ibeacons
```
*or via yarn:*
```javascript:
yarn install react-native-ibeacons
```
### 2. link to your application

```javascript
react-native link react-native-ibeacons
```
### 3.a configuration specific to iOS

> IMPORTANT: you have to set `Bluetooth` and `localization service` in background Modes

![ios: active background mode](./images/bgmode.gif)


### 3.b configuration specific to Android

Nothing (lucky Android :smile:).


## 4 usage

### 4.a iOS

#### Simple example

```javascript
var React = require('react-native');
var {DeviceEventEmitter} = React;

var Beacons = require('react-native-ibeacon');

// Define a region which can be identifier + uuid,
// identifier + uuid + major or identifier + uuid + major + minor
// (minor and major properties are numbers)
var region = {
    identifier: 'Estimotes',
    uuid: 'B9407F30-F5F8-466E-AFF9-25556B57FE6D'
};

// Request for authorization while the app is open
Beacons.requestWhenInUseAuthorization();

Beacons.startMonitoringForRegion(region);
Beacons.startRangingBeaconsInRegion(region);

Beacons.startUpdatingLocation();

// Listen for beacon changes
var subscription = DeviceEventEmitter.addListener(
  'beaconsDidRange',
  (data) => {
    // data.region - The current region
    // data.region.identifier
    // data.region.uuid

    // data.beacons - Array of all beacons inside a region
    //  in the following structure:
    //    .uuid
    //    .major - The major version of a beacon
    //    .minor - The minor version of a beacon
    //    .rssi - Signal strength: RSSI value (between -100 and 0)
    //    .proximity - Proximity value, can either be "unknown", "far", "near" or "immediate"
    //    .accuracy - The accuracy of a beacon
  }
);
```

### 4.b Android

#### Simple example

```javascript
import { DeviceEventEmitter } from 'react-native'
import Beacons from 'react-native-beacons-android'

// Tells the library to detect iBeacons
Beacons.detectIBeacons()

// Start detecting all iBeacons in the nearby
try {
  await Beacons.startRangingBeaconsInRegion('REGION1')
  console.log(`Beacons ranging started succesfully!`)
} catch (err) {
  console.log(`Beacons ranging not started, error: ${error}`)
}

// Print a log of the detected iBeacons (1 per second)
DeviceEventEmitter.addListener('beaconsDidRange', (data) => {
  console.log('Found beacons!', data.beacons)
})
```

#### API

| method| description|
|:-----|:------------|
| **detectCustomBeaconLayout(parser: string): void**| Allows the detection of a custom beacon layout. For example `detectCustomBeaconLayout('m:0-3=4c000215,i:4-19,i:20-21,i:22-23,p:24-24')` allows you to detect iBeacons beacons.|
| **detectIBeacons(): void**| Allows the detection of iBeacons. It's just like calling detectCustomBeaconLayout with the iBeacons layout.|
| **detectEstimotes(): void**| Allows the detection of Estimote beacons. It's just like calling `detectCustomBeaconLayout` with the Estimote layout.|
| **checkTransmissionSupported(): promise**| Checks if the device can use the Bluetooth to detect the beacons.|
| **setForegroundScanPeriod(period: number): void**| Sets the duration in milliseconds of each Bluetooth LE scan cycle to look for beacons (in foreground). For more info [take a look at the official docs](https://altbeacon.github.io/android-beacon-library/javadoc/index.html)|
| **setBackgroundScanPeriod(period: number): void**| Sets the duration in milliseconds of each Bluetooth LE scan cycle to look for beacons (in background). For more info [take a look at the official docs](https://altbeacon.github.io/android-beacon-library/javadoc/index.html)|
| **setBackgroundBetweenScanPeriod(period: number): void**| Sets the duration in milliseconds spent not scanning between each Bluetooth LE scan cycle when no ranging/monitoring clients are in the foreground. For more info [take a look at the official docs](https://altbeacon.github.io/android-beacon-library/javadoc/index.html)|
| **setHardwareEqualityEnforced(e: boolean): void**| Configures whether the bluetoothAddress (mac address) must be the same for two Beacons to be configured equal. This setting applies to all beacon instances in the same process. Defaults to false for backward compatibility. Useful when all the beacons you are working with have the same UUID, major and minor (they are only uniquely identifiable by their mac address), otherwise the module will detect all the beacons as if they were only one. For more info [take a look at the official docs](https://altbeacon.github.io/android-beacon-library/javadoc/index.html)|
| **getRangedRegions(): promise** | Returns a promise that resolves in an array with the regions being ranged. |
| **getMonitoredRegions(): promise** | Returns a promise that resolves in an array with the regions being monitored. |
| **startMonitoringForRegion({identifier: string, uuid: string, minor: int, major: int}): promise** | Starts monitoring for beacons. The parameter `identifier` must be an unique ID. The parameter uuid is optional, it allows you to detect only the beacons with a specific UUID (if `null` every beacon will be detected). The parameters `minor` and `major` are optional, they allow you to monitor only the region of a specific beacon. |
| **startRangingBeaconsInRegion(regionId: string, beaconsUUID: string): promise** | Starts range scan for beacons. The parameter `regionId` must be an unique ID. The parameter `beaconsUUID` is optional, it allows you to detect only the beacons with a specific UUID (if `null` every beacon will be detected). |
| **stopMonitoringForRegion({identifier: string, uuid: string, minor: int, major: int}): promise** | Stops the monitoring for beacons. |
| **stopRangingBeaconsInRegion(regionId: string, beaconsUUID: string): promise** | Stops the range scan for beacons. |


## license
The MIT License (MIT)

Copyright (c) 2017 Erwan DATIN

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
