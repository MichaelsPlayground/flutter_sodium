# flutter_sodium

https://pub.dev/packages/flutter_sodium

flutter_sodium: ^0.2.0

https://github.com/firstfloorsoftware/flutter_sodium

komplettes Example



Build issue when using Flutter 2.5.0 #67

Hi guys,

when building the example app for iOS Simulator I'm getting this error:

ld: in /Users/peter/Downloads/flutter_sodium-master/example/ios/Pods/../.symlinks/plugins/flutter_sodium/ios/libsodium.a(libsodium_la-aead_chacha20poly1305.o), building for iOS Simulator, but linking in object file built for iOS, file '/Users/peter/Downloads/flutter_sodium-master/example/ios/Pods/../.symlinks/plugins/flutter_sodium/ios/libsodium.a' for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
Which suggests that libsodium.a does not support arm64 architecture.

Anyone can help with this?

Thanks,
Peter



Confirmed. Looks like you're not the only one. As a workaround for now, the following suggestion seems to work -> RxReader/tencent_kit#54 (comment)

Modify the example/ios/Podfile as per the comment in the link. Works on my machine (you may need to remove the Podfile.lock)

Still not sure what's going on.

post_install do |installer|
installer.pods_project.targets.each do |target|
flutter_additional_ios_build_settings(target)
# 兼容 Flutter 2.5
target.build_configurations.each do |config|
#       config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '9.0'
      config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'i386 arm64'
    end
end
end



With flutter_sodium you get access to the modern, easy-to-use [libsodium](https://download.libsodium.org/doc/) crypto library in your [Flutter](https://flutter.io) apps. One set of crypto APIs supporting both Android and iOS.

[![Pub](https://img.shields.io/pub/v/flutter_sodium.svg)](https://pub.dartlang.org/packages/flutter_sodium)

## Getting Started

In your flutter project add the dependency:

```yml
dependencies:
  ...
  flutter_sodium: ^0.2.0
```

Import the plugin and initialize it. Sodium.init() initializes the plugin and should be called before any other function provided by flutter_sodium.

```dart
import 'package:flutter_sodium/flutter_sodium.dart';

// initialize sodium
Sodium.init();
```

## Usage example

```dart
// Password hashing (using Argon)
final password = 'my password';
final str = PasswordHash.hashStringStorage(password);

print(str);

// verify hash str
final valid = PasswordHash.verifyStorage(str, password);

assert(valid);
```

This project includes an extensive example app with runnable code samples. Be sure to check it out!

<img src="https://raw.githubusercontent.com/firstfloorsoftware/flutter_sodium/master/example/assets/screenshots/screenshot1.png" width="300">

## API coverage
The flutter_sodium plugin implements the following libsodium APIs:
- crypto_aead
- crypto_auth
- crypto_box
- crypto_generichash
- crypto_hash
- crypto_kdf
- crypto_kx
- crypto_onetimeauth
- crypto_pwhash
- crypto_scalarmult
- crypto_secretbox
- crypto_secretstream
- crypto_shorthash
- crypto_sign
- crypto_stream
- randombytes
- sodium_version

API coverage is not 100% complete, track the progress in [issue #61](https://github.com/firstfloorsoftware/flutter_sodium/issues/61)

## Dart APIs
The plugin includes a core API that maps native libsodium functions 1:1 to Dart equivalents. The core API is available in the class [`Sodium`](https://github.com/firstfloorsoftware/flutter_sodium/blob/master/lib/flutter_sodium.dart). Dart naming conventions are used for core API function names. A native libsodium function such as `crypto_pwhash_str`, is available in flutter as `Sodium.cryptoPwhashStr`.

Also included in flutter_sodium is a high-level, opinionated API providing access to libsodium in a Dart friendly manner. The various functions are available in separate Dart classes. Password hashing for example is available in the `PasswordHash` class. The high-level API depends on the core API to get things done.

## Migrating to fluttter_sodium FFI
The FFI implementation of flutter_sodium is backwards incompatible with the previous platform channel implementation. The list of changes:
- the entire FFI API is now synchronous, while the previous implementation was entirely asynchronous
- all hardcoded libsodium constants are now available as properties on the Sodium class.
- in the platform channel versions the Android and iOS implementations were not in sync. Some functions were available only in iOS, others only in Android. With the FFI implementation, there is a single API covering both platforms.

## Background threads
Since the entire FFI API is synchronous, you'll need to do some extra work to execute long running crypto function on a background thread. Luckily this is very easy with Flutter's [compute function](https://api.flutter.dev/flutter/foundation/compute.html).

The following code snippet demonstrates running a password hash on the background thread.

```dart
final pw = 'hello world';
final str = await compute(PasswordHash.hashStringStorageModerate, pw);

print(str);
```
