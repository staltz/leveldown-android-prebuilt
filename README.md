# leveldown-android-prebuilt

A simple fork of [leveldown](https://github.com/Level/leveldown) that adds
Android (non-cross) [compilation](https://github.com/Level/leveldown/pull/411)
and publishes the native prebuilt libraries on npm.

**This is a hacky and temporary solution**

Eventually, we should just use the official `leveldown` with
**cross-compilation** to Android.

## How to use this package

We assume you are using `react-native-node` where your Node.js process will
require leveldown, but this package may work with other Node.js-in-Android
approaches.

You either need to change all explicit `require('leveldown')` to
`require('leveldown-android-prebuilt')` or use a replacement preprocessor, for
instance `noderify --replace.leveldown=leveldown-android-prebuilt` if you are
using [noderify](https://github.com/dominictarr/noderify).

If you are using `react-native-node` then you are using React Native (_...you
don't say_) and leveldown depends on the shared library `libc++_shared.so`. You
can find this `.so` from Termux under `/usr/lib` and you can copy it to your
computer using `scp` (see the maintainer's guidelines below). In your Android
project you should insert that `.so` under
`android/app/src/main/jniLibs/armeabi-v7a/` (or some other
`...armeabiSOMETHING`) and then `react-native-node` v3.3.0+ knows how to find
the `.so` in runtime.

## How to maintain this package

This package is intended to work with
[react-native-node](https://github.com/staltz/react-native-node) which currently
(as of react-native-node v3.3.0) runs Node.js v7.1.0. This is why we will
compile leveldown targetting 7.1.0.

1. Install and open Termux on an Android device
2. Run `uname -m` and check whether the device is armv7 or arm64
3. git clone this repo in Termux
4. Run `prebuild --verbose --target=7.1.0`
5. Set up SSH access to that device (necessary for the next step)
6. `scp` copy
   `/data/data/com.termux/files/home/leveldown/build/Release/leveldown.node` to
   your computer
7. Put that `leveldown.node` in this repo's directory
   `./compiled/7.1.0/android/armSOMETHING` where `armSOMETHING` is `arm` or
   `arm64` depending on what you got from step 2
8. npm publish this repo
