---
title: ubuntu alsa sound output
date: 2019-10-27
categories:
- Tech
- Methodology
tags:
- blog
- post
---

## requirement

- alsa-base
- pulseaudio
- pavucontrol
- blueman
- bluez`*`

Use -`sudo apt install alsa-base pulseaudio pavucontrol blueman bluez*`-
to install requirement packages.

Then reboot the Ubuntu system.


## first

Use -`sudo alsa force-reload`-
to restart alsa system.

Then reboot the Ubuntu system.

## second

Execute -`pavucontrol`-
to selete the output device or output stream.


