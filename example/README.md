# flutter_sodium_example

funktioniert

https://pub.dev/packages/flutter_sodium

flutter_sodium: ^0.2.0

https://github.com/firstfloorsoftware/flutter_sodium

komplettes Example

Demonstrates how to use the flutter_sodium plugin.


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

WICHTIG: podfile.lock musste gelöscht werden !