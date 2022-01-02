# VerificaC19 package for Flutter

[![Flutter](https://github.com/mastro993/verificac19_flutter/actions/workflows/flutter-ci.yml/badge.svg?branch=master)](https://github.com/mastro993/verificac19_flutter/actions/workflows/flutter-ci.yml)
[![pub package](https://img.shields.io/pub/v/verificac19.svg)](https://pub.dev/packages/verificac19)
[![pub points](https://badges.bar/verificac19/pub%20points)](https://pub.dev/packages/verificac19/score)
[![popularity](https://badges.bar/verificac19/popularity)](https://pub.dev/packages/verificac19/score)
[![likes](https://badges.bar/verificac19/likes)](https://pub.dev/packages/verificac19/score)

## About

This package allows to decode and validate any EU Digital Green Certificate in your Flutter application. It is based on the specications contained in the [official it-dgc-verificac19-sdk-android repository](https://github.com/ministero-salute/it-dgc-verificac19-sdk-android).

This library requires an internet connection to downlod and cache rules, CRL and DSCs at least once per day. Once updated the entire process of validation can be done completely offline and in real-time.

Note: this library currently only supports the download of rules, CRL and DSCs from the Italian Backend (https://get.dgc.gov.it/v1/dgc)

## Development & testing

- Clone the repository

```sh
git clone https://github.com/mastro993/verificac19_flutter.git
```

- Get packages

```sh
cd verificac19_flutter

flutter pub get
```

- QR codes for testing can be obtained from this link: https://dgc.a-sit.at/ehn/testsuite

## Installation

```sh
flutter pub add verificac19
```

## Usage

### Setup

First thing to do is to initialize the package. This allows to all internal initializations to be done.

```dart
await VerificaC19.initialize();
```

### Download and cache rules, CRL data and DSCs

You can download and cache rules, CRL data and DSCs using the `update` function. This will update data only if the 24 hours update window is expired.

```dart
await VerificaC19.update();
```

You can optionally pass the `force` parameter to force the update before the **24 hours** expiry window, but only after **1 hour** from the last update.

```dart
await VerificaC19.update(force: true);
```

You can also check if the data is expired (older than the 24 hours update window) without requiring an update with the `needUpdate` function.

```dart
bool requiresUpdate = await VerificaC19.needsUpdate();
```

### Verify and validate a DGC

You can verify and validate a DGC from a String containing a base45 encoded data.

```dart
// Validate frombase45 encoded data
ValidationResult result = await VerificaC19.validateFromRaw('HC1:NCFOXN%TSMAHN-H3ZSUZK+.V0ET9%6-AH...');
```

`ValidationResult` object contains a `CertificateStatus` which can have the following values:

|     | Code        | Description                  |
| --- | ----------- | ---------------------------- |
| ✅  | valid       | Certificate is valid         |
| ❌  | notValid    | Certificate is not valid     |
| ❌  | notValidYet | Certificate is not valid yet |
| ❌  | notEuDCC    | Certificate is not an EU DCC |
| ❌  | revoke      | Certificate has been revoked |

You can also provide a `ValidationMode` parameter.

| Code       | Description                         |
| ---------- | ----------------------------------- |
| normalDGP  | Normal verification (default value) |
| superDGP   | Super Green Pass verification       |
| boosterDGP | SBooster verification mode          |

Example:

```dart
ValidationResult result = await VerificaC19.validateCertificate(cert, mode: ValidationMode.normalDGP);
// or
ValidationResult result = await VerificaC19.validateCertificate(cert, mode: ValidationMode.superDGP);
```

## Examples

An example application is currently WIP.

## TODOs

- [ ] Add the ability to retrieve/validate a Certificate from an image file
- [ ] Add the ability to implement rules providers from other countries
