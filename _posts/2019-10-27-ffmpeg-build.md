---
title: ffmpeg build on ubuntu
date: 2019-04-08
categories:
- Tech
- Methodology
tags:
- blog
- post
---

> ffmpeg build on ubuntu


## first

install some packages
```
sudo apt install libavc1394-dev
sudo apt install librom1394
sudo apt install libsmbclient-dev
sudo apt install    libsnappy-dev
sudo apt install    libsoxr-dev
sudo apt install      libssh-dev
sudo apt install    libspeex-dev
sudo apt install     libsrtp2-dev    mediainfo mediainfo-gui
sudo apt install       srtp-utils
sudo apt install nasm
sudo apt install ysam
sudo apt install ctags
sudo apt install build-essential
sudo apt install libopenal-dev
sudo apt install libchromaprint-dev
sudo apt install ladspa-sdk
sudo apt install libfrei0r-ocaml-dev
sudo apt install libgmp3-dev
sudo apt install libgcrypt20-dev
sudo apt install libfreetype6-dev
sudo apt install libmp3lame-dev
sudo apt install opencv
sudo apt install libopencv-dev
sudo apt install liblilv-dev
sudo apt install libiec61883-dev
sudo apt install libass-dev
sudo apt install libbluray-dev
sudo apt install libbs2b-dev
sudo apt install libopus-dev
sudo apt install libcaca-dev
sudo apt install libcodec2-dev
sudo apt install libflite
sudo apt install libflite1
sudo apt install libgme-dev
sudo apt install libgsm1-dev
sudo apt install liblensfun-dev
sudo apt install libmodplug-dev
sudo apt install libmysofa-dev
sudo apt install     librtmp-dev
sudo apt install   librubberband-dev
sudo apt install     libshine-dev
sudo apt install  libtesseract-dev
sudo apt install  tesseract-dev*
sudo apt install  tesseract-ocr-*
sudo apt install    libtheora-dev
sudo apt install libtlsh-dev
sudo apt install  libtwolame-dev
sudo apt install   libv4l-dev
sudo apt install   libvorbis-dev
sudo apt install  libvpx-dev
sudo apt install libwavpack-dev
sudo apt install  libwebp-dev
sudo apt install  libx264-dev
sudo apt install libx265-dev
sudo apt install x265   libx265-doc
sudo apt install   libxvidcore-dev
sudo apt install  libzvbi-dev
sudo apt install libjack-dev
sudo apt install libcdio-dev
sudo apt install libcdio-cdda-dev   libcdio-paranoia-dev 
sudo apt install libsdl2-dev libsdl2-gfx-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-net-dev libsdl2-ttf-dev
sudo apt install libsdl2-dev libsdl2-gfx-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-net-dev libsdl2-ttf-dev
sudo apt install vlc
```

## second

use this command to configure
```
./configure  \
  --enable-ffplay  \
  --enable-version3  \
  --enable-gpl  \
  --enable-nonfree  \
  --enable-avresample   \
  --enable-avisynth  \
  --enable-chromaprint  \
  --enable-frei0r  \
  --enable-gcrypt  \
  --enable-gmp  \
  --enable-gcrypt  \
  --enable-ladspa  \
  --enable-libass  \
  --enable-libbluray  \
  --enable-libbs2b  \
  --enable-libcaca  \
  --enable-libcdio  \
  --enable-libcodec2  \
  --enable-libdc1394  \
  --enable-libfontconfig  \
  --enable-libfreetype  \
  --enable-libfribidi  \
  --enable-libgme  \
  --enable-libgsm  \
  --enable-libiec61883  \
  --enable-libjack  \
  --enable-liblensfun  \
  --enable-libmodplug  \
  --enable-libmp3lame  \
  --enable-libopus  \
  --enable-librubberband  \
  --enable-librtmp  \
  --enable-libshine  \
  --enable-libsmbclient  \
  --enable-libsnappy  \
  --enable-libsoxr  \
  --enable-libspeex  \
  --enable-libssh  \
  --enable-libtesseract  \
  --enable-libtheora  \
  --enable-libtwolame  \
  --enable-libv4l2    \
  --enable-libvorbis  \
  --enable-libvpx  \
  --enable-libwavpack  \
  --enable-libwebp  \
  --enable-libx264  \
  --enable-libx265  \
  --enable-libxcb  \
  --enable-libxvid  \
  --enable-libxml2  \
  --enable-libzmq  \
  --enable-libzvbi  \
  --enable-lv2  \
  --enable-openal  \
  --enable-opengl  \
  --enable-openssl  \
  --enable-libdrm  \
  --enable-linux-perf \
  --enable-pic
```

## third

do make
```
make -j 4
```


## Note

If no sdl2 in system, ffplay would not to build.
So, need to install sdl2 package if you want ffplay.

To see `ffplay_deps` in `configure` file.


