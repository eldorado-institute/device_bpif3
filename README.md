# device_bpif3
Device build config for Banana Pi F3 (SpacemiT K1)

## Setup build envoronment
  https://source.android.com/setup/build/initializing

## Download Android source
  https://github.com/eldorado-institute/local_manifests

## Build Android
 Refer to http://source.android.com/source/building.html
```
  $ source build/envsetup.sh
  $ lunch bpif3-eng
  $ make ramdisk systemimage vendorimage
```
## Prepare sd card
 Partitions of the card should be set-up like followings
 ```
  p1:  128MB for boot      : Do fdisk, mkfs.vfat, set EFI type
  p2:  128MB for /vendor   : Do fdisk, new primary partition
  p3: 1024MB for /system   : Do fdisk, new primary partition
  p4: remainings for /data : Do fdisk, mkfs.ext4

 Set volume label of /data partition as userdata : use -L option for mkfs.ext4
```

## Write system & vendor partition
```
  $ cd out/target/product/bpif3
  $ sudo dd if=vendor.img of=/dev/<p2> bs=1M
  $ sudo dd if=system.img of=/dev/<p3> bs=1M
```

## Copy firmware & ramdisk to boot partition
```
  device/eld/bpif3/boot/* to p1:/
  out/target/product/bpif3/ramdisk.img to p1:/
```

## Download & Build kernel
 Install cross-compiler
 ```
  $ sudo apt install gcc-riscv64-linux-gnu
 ```
 Git clone following kernel source under separate folder apart from Android

  https://github.com/eldorado-institute/ack-bpfi3

 Build the kernel
 ```
  $ ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- make k1_defconfig
  $ ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- make -j16
```
## Copy kernel binaries to boot partition
```
  <kernel directory>/arch/riscv/boot/Image.itb to p1:/
  <kernel directory>/arch/riscv/boot/dts/spacemit/k1-x_deb1.dtb to p1:/dtb/spacemit/
```
