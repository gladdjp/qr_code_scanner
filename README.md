# ！！これはFork版です。
Pull Requestを出すときは、マージ先に注意しましょう。
新規リポジトリからPushしたときに、自動的に表示されるPull requestボタンで作成すると、マージ先がOriginalのブランチになってしまいます。
間違って作ってしまったらGitHubのサポートページで依頼しましょう。

# Project in Maintenance Mode Only

Since the underlying frameworks of this package, [zxing for android](https://github.com/zxing/zxing) and [MTBBarcodescanner for iOS](https://github.com/mikebuss/MTBBarcodeScanner) are both not longer maintaned, this plugin is no longer up to date and in maintenance mode only. Only bug fixes and minor enhancements will be considered.

I am developing a new plugin [mobile_scanner](https://pub.dev/packages/mobile_scanner) that uses the latest version of MLKit for detecting barcodes and QR codes. On Android it also uses the latest version of CameraX, and on iOS the native AVFoundation for best camera performance. 

# QR Code Scanner

[![pub package](https://img.shields.io/pub/v/qr_code_scanner?include_prereleases)](https://pub.dartlang.org/packages/qr_code_scanner)
[![Join the chat](https://img.shields.io/discord/829004904600961054)](https://discord.gg/aZujk84f6V)
[![GH Actions](https://github.com/juliuscanute/qr_code_scanner/workflows/dart/badge.svg)](https://github.com/juliuscanute/qr_code_scanner/actions)

A QR code scanner that works on both iOS and Android by natively embedding the platform view within Flutter. The integration with Flutter is seamless, much better than jumping into a native Activity or a ViewController to perform the scan.

## Screenshots
<table>
<tr>
<th colspan="2">
Android
</th>
</tr>

<tr>
<td>
<p align="center">
<img src="https://raw.githubusercontent.com/juliuscanute/qr_code_scanner/master/.resources/android-app-screen-one.jpg" width="30%" height="30%">
</p>
</td>
<td>
<p align="center">
<img src="https://raw.githubusercontent.com/juliuscanute/qr_code_scanner/master/.resources/android-app-screen-two.jpg" width="30%" height="30%">
</p>
</td>
</tr>

<tr>
<th colspan="2">
iOS
</th>
</tr>

<tr>
<td>
<p align="center">
<img src="https://raw.githubusercontent.com/juliuscanute/qr_code_scanner/master/.resources/ios-app-screen-one.png" width="30%" height="30%">
</p>
</td>
<td>
<p align="center">
<img src="https://raw.githubusercontent.com/juliuscanute/qr_code_scanner/master/.resources/ios-app-screen-two.png" width="30%" height="30%">
</p>
</td>
</tr>

</table>

## Get Scanned QR Code

When a QR code is recognized, the text identified will be set in 'result' of type `Barcode`, which contains the output text as property 'code' of type `String` and scanned code type as property 'format' which is an enum `BarcodeFormat`, defined in the library.

```dart
class _QRViewExampleState extends State<QRViewExample> {
  final GlobalKey qrKey = GlobalKey(debugLabel: 'QR');
  Barcode? result;
  QRViewController? controller;

  // In order to get hot reload to work we need to pause the camera if the platform
  // is android, or resume the camera if the platform is iOS.
  @override
  void reassemble() {
    super.reassemble();
    if (Platform.isAndroid) {
      controller!.pauseCamera();
    } else if (Platform.isIOS) {
      controller!.resumeCamera();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: <Widget>[
          Expanded(
            flex: 5,
            child: QRView(
              key: qrKey,
              onQRViewCreated: _onQRViewCreated,
            ),
          ),
          Expanded(
            flex: 1,
            child: Center(
              child: (result != null)
                  ? Text(
                      'Barcode Type: ${describeEnum(result!.format)}   Data: ${result!.code}')
                  : Text('Scan a code'),
            ),
          )
        ],
      ),
    );
  }

  void _onQRViewCreated(QRViewController controller) {
    this.controller = controller;
    controller.scannedDataStream.listen((scanData) {
      setState(() {
        result = scanData;
      });
    });
  }

  @override
  void dispose() {
    controller?.dispose();
    super.dispose();
  }
}

```

## Android Integration
In order to use this plugin, please update the Gradle, Kotlin and Kotlin Gradle Plugin:

In ```android/build.gradle``` change ```ext.kotlin_version = '1.3.50'``` to ```ext.kotlin_version = '1.5.10'```

In ```android/build.gradle``` change ```classpath 'com.android.tools.build:gradle:3.5.0'``` to ```classpath 'com.android.tools.build:gradle:4.2.0'```

In ```android/gradle/wrapper/gradle-wrapper.properties``` change ```distributionUrl=https\://services.gradle.org/distributions/gradle-5.6.2-all.zip``` to ```distributionUrl=https\://services.gradle.org/distributions/gradle-6.9-all.zip```

In ```android/app/build.gradle``` change 
```defaultConfig{```
  ```...```
  ```minSdkVersion 16```
```}``` to 
```defaultConfig{```
  ```...```
  ```minSdkVersion 20```
```}```

### *Warning*
If you are using Flutter Beta or Dev channel (1.25 or 1.26) you can get the following error:

`java.lang.AbstractMethodError: abstract method "void io.flutter.plugin.platform.PlatformView.onFlutterViewAttached(android.view.View)"`

This is a bug in Flutter which is being tracked here: https://github.com/flutter/flutter/issues/72185

There is a workaround by adding `android.enableDexingArtifactTransform=false` to your `gradle.properties` file.

## iOS Integration
In order to use this plugin, add the following to your Info.plist file:
```
<key>io.flutter.embedded_views_preview</key>
<true/>
<key>NSCameraUsageDescription</key>
<string>This app needs camera access to scan QR codes</string>
```

## Web Integration

Add this to `web/index.html`:

```html
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.3.1/dist/jsQR.min.js"></script>
```

Please note: on web, only QR codes are supported. Other barcodes and 2D codes cannot be scanned.

Web support is in very early stage. Features such as flash, pause or resume are not implemented. Moreover, the camera 
preview does not respect the surrounding constraints. This is not at last due to Flutter's early state of platform views
on web.

## Flip Camera (Back/Front)
The default camera is the back camera.
```dart
await controller.flipCamera();
```

## Flash (Off/On)
By default, flash is OFF.
```dart
await controller.toggleFlash();
```

## Resume/Pause
Pause camera stream and scanner.
```dart
await controller.pauseCamera();
```
Resume camera stream and scanner.
```dart
await controller.resumeCamera();
```


# SDK
Requires at least SDK 20.
Requires at least iOS 8.

# TODOs
* iOS Native embedding is written to match what is supported in the framework as of the date of publication of this package. It needs to be improved as the framework support improves.
* In future, options will be provided for default states.
* Finally, I welcome PR's to make it better :), thanks

# Credits
* Android: https://github.com/zxing/zxing
* iOS: https://github.com/mikebuss/MTBBarcodeScanner
* Special Thanks To: LeonDevLifeLog for his contributions towards improving this package.
