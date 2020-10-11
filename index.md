## Purpose of Covid App

  Successful containment of the Coronavirus pandemic rests on the ability to quickly and
reliably identify those who have been in close proximity to a contagious individual. Existing tools
for doing so rely on the collection of exact location information of individuals over lengthy time
periods, and combining this information with other personal information. This unprecedented
encroachment on individual privacy at national scales has created an outcry and risks rejection
of these tools.

We propose an alternative: an extremely simple scheme for providing fine-grained and timely
alerts to users who have been in the close vicinity of an infected individual. Crucially, this is done
while preserving the anonymity of all individuals, and without collecting or storing any personal
information or location history. Our approach is based on using short-range communication
mechanisms, like Bluetooth, that are available in all modern cell phones. It can be deployed
with very little infrastructure, and incurs a relatively low false-positive rate compared to other
collocation methods. We also describe a number of extensions and tradeoffs.

We believe that the privacy guarantees provided by the scheme will encourage quick and
broad voluntary adoption. When combined with sufficient testing capacity and existing best
practices from healthcare professionals, we hope that this may significantly reduce the infection
rate.


## Start Up Guide

### Install Prerequisites

* For Mac OS, install `Node` and `Wacthman` on `Homebrew` for Android device

```
brew install node
brew install watchman
```
* Install JDK using homebrew

```
brew cask install adoptopenjdk/openjdk/adoptopenjdk8
```
* Install Android Development Environment
 * Install Android Studio
 * Install Android SDK
 * Configure ANDROID_HOME environment variable

### Create application

```
npx react-native init AwesomeProject
```

### Running application
Note: to run on virtual device, open `./AwesomeProject/android` and choose an available AVD. 

* Start Metro, JavaScript bundler (collects and connects all dependencies)

* Start Application:
```
npx react-native run-android
```
Note: to run from CLI terminal
* Clone repo
* Install Dependencies
```
yarn install
```
* Start app
``` 
yarn android
or
npx react-native run-android
```

## Proximity Modules 

### Bluetooth Packets

Has 40 channels in 2.4 GHz separated by 2MHz. Two types of transmissions: data (47 channels) and advertising (3 channels). 

Packet Format:

```
preamble (1 octet) | access address (4 octets) | PDU (2-257 octets) | CRC (3 octets)

#Protocol Data Unit: payload + header
#Cyclic Redundancy Check: Error checking
```

Advertising PDU: used to broadcast before connection. Consist of 16 bit header + variable-sized payload

Header format includes `Length` and `PDU Type` segments (in total 6 segments). There 4 `PDU Types`:
* `ADV_IND` (to any device)
* `ADV_DIRECT_IND` (to specific device)
* `ADV_NONCONN_IND`(advertise info to any listeners)
* `ADV_SCAN_IND` (advertise additional info to a device after permissions)

Long term connection: `ADV_IND` and `ADV_DIRECT_IND`
Short term connection: `ADV_NONCONN_IND` and `ADV_SCAN_IND`

### Bluetooth Scans

Using `react-native-plx` library to support bluetooth scans:

```
bleManager.startDeviceScan(
  UUIDs: ?Array<UUID>,
  options: ?ScanOptions,
  listener: (error: ?Error, scannedDevice: ?Device) => void
)
```

* `UUID`:Array of strings containing `UUID` of different services. If `null`, all devices are scanned

* `ScanOptions`: Optional configs ie. `allowDuplicates?: boolean` duplicate scanning record to be received frequently on IOS or emitted on Android

* `listener`: called this function for every device scanned. First argument set to non-null value if scanning failed. Second argument is the scanne devices. 

To end scans:
```
bleManager.stopDeviceScan()
```

In order to scan:
* For IOS: `UUID` array must be non-empty array during background scanning. App performs different in background due to limited system resources thus Core Bluetooth tasks are disabled thus must declare app to support Core Bluetooth background modes and wake up app to process Bluetooth events. Cannot run forever because it will be terminated to free up memory thus lose connection. 
```
(void)scanForPeripheralsWithServices:(NSArray<CBUUID *> *)serviceUUIDs 
                               options:(NSDictionary<NSString *,id> *)options;
```

* For Android: Need `ACCESS_COARSE_LOCATION` or `ACCESS_FINE_LOCATION` runtime permission for Android 6. Only `ACCESS_FINE_LOCATION` for IOS since Android 10.OS may kill application periodically during backgroudnd scanning thus app must be woken up periodically. BLE scan can cause issues on different android devices. 

### Running Background Apps (iOS) 
*In progress: focusing on android first*

### Running in Peripheral Mode

In peripheral mode, he app's embedded sesnors broadcasts advertising PDUs 50ms -2s intervels while Central scans from the app seeks this data.  

To put android app into peripheral mode, install library (*in progress*) 
```
npm i react-native-ibeacon-simulator
```

## Backend Development

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/edsun123/covidApp/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

## Frontend Development

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
