# Third-party frameworks in the Skillz Swift package

The Skillz package embeds these frameworks unmodified, because Skillz.framework loads them at
launch. They are the binaries the matching CocoaPods install. Each one is under its own license;
the texts are in this folder.

| Framework | Version | Source | License | File |
| --- | --- | --- | --- | --- |
| RiveRuntime | 5.10.0 | RiveRuntime CocoaPod | MIT | RiveRuntime-LICENSE.txt |
| OpenTelemetryApi | 1.13.1 | OpenTelemetrySwiftApi CocoaPod | Apache License 2.0 | OpenTelemetryApi-LICENSE.txt |
| KochavaCore, KochavaTracker | 6.1.1 | Apple-Cocoapod-KochavaCore / -KochavaTracker CocoaPods | Kochava Terms of Service | Kochava-LICENSE.txt |
| AppLovinSDK | 13.5.1 | AppLovinSDK CocoaPod | AppLovin commercial license | AppLovinSDK-LICENSE.txt |

Keep this folder in step with the dependency versions in Package.swift.template and
Skillz.podspec.
