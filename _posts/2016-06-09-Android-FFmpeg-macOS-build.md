---
layout: post
title: Android FFmpeg macOS에서 빌드하기
---
인터넷에 안드로이드 FFmpeg 빌드하기 내용을 찾아보았다.  
거의다 Linux거나 Windows의 cygwin으로 빌드하는 방법이 나온다.  

그런데 MacOSX에서도 직접 빌드가 가능하다.  
그리고 예전의 버전들에 비해서 빌드가 한결 간편해 졌다.  
사용하는 버전은 다음과 같다.  

Android NDK: r11c  
ffmpeg: 3.0.2  
MacOSX: El Capitan  10.11.5  

먼저 다음 디렉토리에 ffmpeg 소스를 받아서 압축을 푼다.  
~/android-ndk-r11c/sources/ffmpeg-3.0.2$  

그리고 다음과 같이 config.sh를 만들어서 실행한다.  
여기서 중요한 것은 다른 예제에서는 --target-os=linux로 설명하는데 안드로이드가 리눅스라도 그냥 빌드하게되면 .so파일 뒤에 .so.5.24 이런식으로 major, minor 버전으로 symlink가 걸리게 된다.  
그러므로 반드시 **--target-os=android**로 설정하자.
```sh
#!/bin/bash
NDK=/Users/duongame/android-ndk-r11c
SYSROOT=$NDK/platforms/android-9/arch-arm/
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
function build_one
{
    ./configure \
        --disable-symver \
        --prefix=$PREFIX \
        --enable-shared \
        --disable-static \
        --disable-doc \
        --disable-ffmpeg \
        --disable-ffplay \
        --disable-ffprobe \
        --disable-ffserver \
        --disable-avdevice \
        --disable-doc \
        --disable-symver \
        --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
        --target-os=android \
        --arch=arm \
        --enable-cross-compile \
        --sysroot=$SYSROOT \
        --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
        --extra-ldflags="$ADDI_LDFLAGS" \
        $ADDITIONAL_CONFIGURE_FLAG
    make clean
    make
    make install
}
CPU=arm
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
build_one
```
빌드가 끝나면 다음 디렉토리에 include, lib가 생기는데 이것을 복사해서 ndk빌드할때 사용하던지, 패스를 설정하던지 하면된다.  
~/android-ndk-r11c/sources/ffmpeg-3.0.2/android/arm