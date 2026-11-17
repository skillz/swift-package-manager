# Skillz iOS SDK for Swift Package Manager

Swift Package Manager distribution of the Skillz iOS SDK. It replaces the
[SkillzPod](https://github.com/skillz/SkillzPod) CocoaPod, because CocoaPods trunk becomes
read-only on 2 December 2026 and new Skillz versions cannot be published there.

> **Status:** no version has been released here yet. Until one is, keep using the Skillz
> CocoaPod. Full integration docs: https://developers.skillz.com

This repository holds only `Package.swift`. The SDK and its dependencies are prebuilt
xcframeworks attached to each [release](https://github.com/skillz/swift-package-manager/releases).
Every release is a `MAJOR.MINOR.PATCH` tag that matches the Skillz SDK version.

## What the package contains

The single `Skillz` product links and embeds these dynamic frameworks. Skillz.framework loads
them at launch, so all of them are required:

| Framework | Version |
| --- | --- |
| Skillz | the release version |
| KochavaCore, KochavaTracker | 6.1.1 |
| AppLovinSDK | 13.5.1 |
| RiveRuntime | 5.10.0 |
| OpenTelemetryApi | 1.13.1 |

Minimum deployment target: iOS 15.0.

## Native iOS integration

1. In Xcode, choose **File > Add Package Dependencies**, enter
   `https://github.com/skillz/swift-package-manager`, pick **Exact Version**, and add the
   `Skillz` product to your app target.

   Or in a `Package.swift`:

   ```swift
   .package(url: "https://github.com/skillz/swift-package-manager", exact: "<version>")
   ```

2. Add `GeoComplySDK.xcframework` from the Skillz SDK download to the app target and set it
   to **Embed & Sign**. It is not part of this package.

3. Set these build settings on the app target. A Swift package cannot set them for you:

   | Setting | Value |
   | --- | --- |
   | `EXCLUDED_ARCHS[sdk=iphonesimulator*]` | `arm64` |
   | `LD_RUNPATH_SEARCH_PATHS` | add `/usr/lib/swift` |
   | `ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES` | `YES` |
   | `ENABLE_USER_SCRIPT_SANDBOXING` | `NO` |

   Skillz.xcframework has no arm64 simulator slice, so on Apple silicon Macs the app runs in
   the simulator under Rosetta. Device builds are not affected.

4. Add a **Run Script** build phase to the app target, as the **last** phase, with
   "Based on dependency analysis" turned off:

   ```sh
   SKZ_FW="${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}/Skillz.framework"
   if [ ! -d "${SKZ_FW}" ]; then
     echo "error: Skillz.framework is not embedded in the app. Check that the Skillz package product is added to this target."
     exit 1
   fi
   if [ -e "${SKZ_FW}/postprocess.sh" ]; then
     /bin/sh "${SKZ_FW}/postprocess.sh"
   else
     echo "warning: Skillz postprocess.sh not found. Expected on a repeat archive; otherwise clean the build folder."
   fi
   ```

   The script adds the Info.plist keys, URL schemes, entitlements and App Group the SDK
   needs, and re-signs the Skillz frameworks. It must run after Xcode embeds the frameworks.

## Unity

Enable **Install Skillz via Swift Package Manager** in the Skillz Settings window. The iOS
export then adds this package, the build settings and the Run Script phase to the Xcode
project and removes the Skillz pod from the Podfile. Other SDKs in the game keep using
CocoaPods.

## Migrating from the CocoaPod

1. Remove `pod 'Skillz'` and any Skillz `script_phase` from the Podfile, then run
   `pod install`.
2. Follow the integration steps above.
3. Clean the build folder before the first build.

## For maintainers

Releases are published by the Skillz SDK release job. Do not edit `Package.swift` or tags by
hand. `Package.swift` is generated from
`apps/sdk-mobile/sdk-core/ios/SkillzSDK-iOS/Package.swift.template` in
[sdk-mobile](https://github.com/skillz/sdk-mobile).

Each release, in this order:

1. Commit `Package.swift` with the asset URLs and their `swift package compute-checksum`
   values.
2. Create a **draft** GitHub release for the new tag on that commit and upload the
   xcframework zips.
3. Publish the release. Publishing creates the tag, so the tag never points at a package
   whose assets are missing.

Tags and assets of a published version are never replaced. Fix a bad release with a new
version.
