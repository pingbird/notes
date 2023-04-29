---
share: true
title: "Keyboards"
---

A few months ago I got my first keyboard that supports custom firmware, the [Keychron K3 Pro](https://www.keychron.com/products/keychron-k3-pro-qmk-via-wireless-custom-mechanical-keyboard) and it's really good. The layout is nice and has a function row unlike the K7, it was a little annoying having to do shift-fn-\` to type tildes along with some IDE binds like fn-shift-6 for refactoring.

Out of the box the RGB on the K3 pro sucks, the (otherwise high quality) keycaps it comes with are opaque and have a wide base which blocks out the light, also the maximum brightness is ridiculously low.

Thankfully you can buy a separate set of [translucent ABS keycaps](https://www.keychron.com/products/low-profile-abs-full-set-keycap-set?variant=40332974948441) which shine through much better, and do some QMK firmware tweaks to increase the maximum current. The default [CKLED2001_CURRENT_TUNE](https://github.com/Keychron/qmk_firmware/blob/e4f4ceaf3f2e3d25fb282273a81f9b58790fc427/keyboards/keychron/k3_pro/ansi/rgb/config.h#L104) is set to 0x14, or about 8% so I boosted it to 0x34 or about 20%.

## Keycaps

As any perfectly sane person with a lot of time would do, I sanded off the top all the keycaps by hand:

![[20230421_015107.jpg]]

![type:video](20230416_151953_3.mp4)

It looks soooooooooooooooooooooooooooooo pretty

## Unicode

Another cool thing I did with the keyboard is implement the [agda emacs input mode](https://agda.readthedocs.io/en/latest/tools/emacs-mode.html) on the keyboard hardware itself, no pynput or AHK involved.

awoo