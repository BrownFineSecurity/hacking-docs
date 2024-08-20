
## Get APK from device over adb

find apk file on device:
```
adb shell
pm list packages -f | grep -i <appname>
```

pull apk file from device:
```
adb pull <path>
```

## unpack and modify APK to add frida libgadget


unpack apk with apktool:
```
apktool d -o unpack app.apk
```

download frida gadget native lib + extract and rename:
```
cd unpack/lib/arm64-v8a
wget https://github.com/frida/frida/releases/download/16.1.5/frida-gadget-16.1.5-android-arm64.so.xz
unxz frida-gadget-16.1.5-android-arm64.so.xz
mv frida-gadget-16.1.5-android-arm64.so libfrida-gadget.so
```

find LAUNCHER activity in manifest:
```
TODO
```

search for LAUNCHER activity smali code:
```
TODO
```

look for the following method:
```
.method public constructor <init>()V
```

add the following smali code to the above constructor:
```
const-string v0, "frida-gadget"
invoke-static {v0}, Ljava/lang/System;->loadLibrary(Ljava/lang/String;)V
```


add to manifest application element:
```
android:usesCleartextTraffic="true"
android:extractNativeLibs="true"
```

## Generate a JKS for APK signing

run keytool to generate java keystore (JKS) for apk signing:
```
keytool -genkey -v -keystore custom.keystore -alias mykeyaliasname -keyalg RSA -keysize 2048 -validity 10000
```

## Repackage APK

repack APK:
```
apktool b --use-aapt2 -o mod-app.apk unpack/
```

zipalign APK:
```
zipalign 4 mod-app.apk final-mod-app.apk
```

sign APK file with JKS:
```
apksigner sign -ks custom.keystore final-mod-app.apk
```

## Install APK to phone

install APK with adb:
```
adb install final-mod-app.apk
```

## Frida scripts for bypass pinning

Sometimes one of these scripts will fail for some reason.

frida ssl bypass scripts:
```
frida -U gadget --codeshare akabe1/frida-multiple-unpinning
frida -U gadget --codeshare sowdust/universal-android-ssl-pinning-bypass-2
```
