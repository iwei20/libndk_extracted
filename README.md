# libndk_extracted
Files extracted from Android SDK for x86_64, version 30, revision 9, that are related to the libndk translation layer. README contains instructions for installation onto an existing Waydroid image.

The Android image these files were extracted from can be found at https://dl.google.com/android/repository/sys-img/google_apis_playstore/x86_64-30_r09-linux.zip.

## Installation into Waydroid
1) Make a copy of the system image `/var/lib/waydroid/images/system.img`. In this guide the copy is called `system.img`.
2) Make a directory to serve as a mountpoint for the system image. In this guide the directory is called `mnt`.
3) Resize `system.img` to be ~500MB larger, to make space for the new library files (this is probably a severe overapproximation).

The current size of the image can be checked with:
```sh
ls -l --block-size=MB
```

Resizing can be done with
```sh
sudo resize2fs system.img [new size] 
```

4) Mount `system.img` onto the mountpoint `mnt`.
```sh
sudo mount system.img mnt
```

5) Add the library files to their appropriate locations in the mounted filesystem. The absolute path of the destination should match their relative path in this repository. For example, the file in this repository located at
```
system/lib64/libndk_translation.so
```
should land in
```
/system/lib64/libndk_translation.so
```

Tip: Use `sudo cp` or `sudo mv` to perform these moves.

6) Unmount the system image.
```
sudo umount mnt
```

7) Overwrite `/var/lib/waydroid/images/system.img` with your newly modified `system.img`.
8) Add the following properties below the `[properties]` header in `/var/lib/waydroid/waydroid.cfg`:

TODO: The ndk_translation version is unconfirmed.
```
ro.product.cpu.abilist = x86_64,x86,armeabi-v7a,armeabi,arm64-v8a
ro.product.cpu.abilist32 = x86,armeabi-v7a,armeabi
ro.product.cpu.abilist64 = x86_64,arm64-v8a
ro.dalvik.vm.native.bridge = libndk_translation.so
ro.enable.native.bridge.exec = 1
ro.vendor.enable.native.bridge.exec = 1
ro.vendor.enable.native.bridge.exec64 = 1
ro.ndk_translation.version = 0.2.3
ro.dalvik.vm.isa.arm = x86
ro.dalvik.vm.isa.arm64 = x86_64
```

9) Upgrade your Waydroid container to reflect the new config.
```
sudo waydroid upgrade
```

## Credits
- [sickcodes/Droid-NDK-Extractor](https://github.com/sickcodes/Droid-NDK-Extractor) for the script that I followed manually to extract the files.
- [casualsnek/waydroid_script](https://github.com/casualsnek/waydroid_script/) for list of properties.

