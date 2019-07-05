---
layout: post
title:  Android编译ffmpeg,x264,opencv
date:   2019-7-01
author: husong
header-img: img/post-book.jpg
catalog: true
tags:
    - 技术
---

#### 下载源码
> OpenCV 源码 
[https://opencv.org/releases/](https://opencv.org/releases/)

> FFMPEG 源码
[https://ffmpeg.org/download.html#build-linux](https://ffmpeg.org/download.html#build-linux)
可以参考[ffmpeg-android脚本的写法](git@github.com:WritingMinds/ffmpeg-android.git)

> x264 源码
[https://www.videolan.org/developers/x264.html](https://www.videolan.org/developers/x264.html)

> NDK 源码
[https://developer.android.com/ndk/downloads/older_releases.html?hl=zh-cn](https://developer.android.com/ndk/downloads/older_releases.html?hl=zh-cn)
> > 需要下载ndk15c, 其它版本的编译可能会出问题, 参考 [ffmpeg编译](https://medium.com/@ilja.kosynkin/building-ffmpeg-4-0-for-android-with-clang-642e4911c31e)

##### OpenCV 编译
配置ANDROID_NDK 环境变量或者脚本指定NDK的路径
```
export ANDROID_NDK=/home/kuiper/Android/Sdk/ndk-bundle
```

编译脚本
```
#!/bin/sh
#export BASE_PATH=${HOME}/Android
export NDK_PATH=/home/kuiper/Downloads/android-ndk-r15c

echo "ndk path:" $NDK_PATH

rm -rf android
mkdir android
cd android

cmake \
         -DANDROID_NDK=${NDK_PATH} \
         -DCMAKE_TOOLCHAIN_FILE=${NDK_PATH}/build/cmake/android.toolchain.cmake \
         -DANDROID_PLATFORM=android-21\
         -DANDROID_STL=c++_static \
         -DANDROID_ABI="armeabi-v7a" \
         -DANDROID_ARM_NEO="ON" \
         -DBUILD_ANDROID_PROJECTS=OFF \
         -DBUILD_STATIC_LIBS=ON \
         -DBUILD_OPENEXR=ON \
         -DANDROID_TOOLCHAIN="clang" \
         -DCMAKE_INSTALL_PREFIX=./android_install \
         -DCMAKE_BUILD_TYPE=Release $@ ..

cmake --build . --config Release --target install -- -j4


         #-DBUILD_SHARED_LIBS=ON \
         #-DBUILD_opencv_world=ON \

```


##### FFMPEG 编译

```
#!/bin/bash
NDK=/home/kuiper/Downloads/android-ndk-r15c
SYSROOT=$NDK/platforms/android-21/arch-arm/
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64

function build_one
{
./configure \
--prefix=$PREFIX \
--enable-shared \
--enable-static \
--disable-doc \
--disable-ffplay \
--disable-ffprobe \
--disable-doc \
--disable-symver \
--disable-linux-perf \
--enable-protocol=concat \
--enable-protocol=file \
--enable-muxer=mp4 \
--enable-demuxer=mpegts \
--cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
--target-os=linux \
--arch=arm \
--enable-cross-compile \
--sysroot=$SYSROOT \
--extra-cflags="-Os -fpic $ADDI_CFLAGS" \
--extra-ldflags="$ADDI_LDFLAGS" \
$ADDITIONAL_CONFIGURE_FLAG
make clean all
make -j4
make install
}
CPU=armeabi-v7a
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
build_one

```



##### x264编译
```
#!/bin/bash

echo "###### start build  x264 ######"

NDK=/home/kuiper/Downloads/android-ndk-r15c
SYSROOT=$NDK/platforms/android-21/arch-arm/
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64

function build_one
{
./configure \
--prefix=$PREFIX \
--cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
--extra-cflags="-Os -fpic $ADDI_CFLAGS" \
--extra-ldflags="$ADDI_LDFLAGS" \
--enable-static \
--enable-pic \
--enable-strip \
--disable-cli \
--host=arm-linux \
--sysroot=$SYSROOT

make clean
make -j4
make install
}

CPU=armeabi-v7a
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
build_one

echo "###### x264 build finish ######"

```


#### SDK工程编译
```
#!/bin/bash
SCRIPT_DIR="$( cd "$(dirname "$0")" ; pwd -P)/../"
echo "SCRIPT_DIR=$SCRIPT_DIR"

SUPPORTED_ARCHITECTURES=(armeabi-v7a) # list (armeabi-v7a armeabi-v7a-neon arm64-v8a x86)
PLATFORM_TYPE="Android"
DECODER_TYPE="cpu" # gpu or cpu

BUILD_DIR=$SCRIPT_DIR/build-$PLATFORM_TYPE
mkdir -p $BUILD_DIR
echo "Build $PLATFORM_TYPE version into: $BUILD_DIR"

if [ -z ${CMAKE_BUILD_TYPE+x} ]; then
CMAKE_BUILD_TYPE=Release
fi

echo "Build Type : $CMAKE_BUILD_TYPE"

cd $BUILD_DIR

for arch in "${SUPPORTED_ARCHITECTURES[@]}"
do
cmake  .. \
    -DCMAKE_INSTALL_PREFIX=$SCRIPT_DIR/release/Android/$arch \
	-DCMAKE_TOOLCHAIN_FILE="$ANDROID_NDK/build/cmake/android.toolchain.cmake" \
	-DANDROID_ABI=$arch \
    -DPLATFORM_TYPE=$PLATFORM_TYPE \
    -DDECODER_TYPE=$DECODER_TYPE \
	-DANDROID_PLATFORM="android-21" \
	-DANDROID_ARM_NEON="ON" \
	-DANDROID_NDK="$ANDROID_NDK" \
	-DCMAKE_BUILD_TYPE=$CMAKE_BUILD_TYPE \
	-DANDROID_TOOLCHAIN="clang" \
	-DANDROID_STL="c++_static" \
    $@ \
    || exit 1

cmake --build . -- "-j$(cat /proc/cpuinfo| grep "processor"| wc -l)"
done

```

#### 3rd_party 目录
```
3rd_party
├── Android
│   ├── ffmpeg
│   │   └── armeabi-v7a
│   ├── opencv
│   │   └── armeabi-v7a
│   └── x264
│       └── armeabi-v7a
└── Linux
    ├── ffmpeg
    │   ├── include
    │   ├── lib
    │   └── share
    ├── opencv3_3_1
    │   ├── include
    │   └── lib
    └── x264
        ├── libx264.a
        ├── libx264.so
        ├── libx264.so.152
        └── x264.h
```

+  需要注意的几个地方
    + libmediandk was first added to android-21  
    + NDK版本 ndkr17 以上版本编译ffmpeg 非常坑爹,建议用r15c版本
    + opencv android 编译后的头文件在  xxx/sdk/jni/include 目录
    + xxxConfig.cmake 用来配置依赖库的路径
