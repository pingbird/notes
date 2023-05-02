---
share: true
title: "Keyboards"
---

## K3 Pro

A few months ago I got my first keyboard that supports custom firmware, the [Keychron K3 Pro](https://www.keychron.com/products/keychron-k3-pro-qmk-via-wireless-custom-mechanical-keyboard) and it's really good. The layout is nice and has a function row unlike the K7, it was a little annoying having to do shift-fn-grave to type tildes along with some IDE binds like fn-shift-6 for refactoring.

Out of the box the RGB on the K3 pro sucks, the (otherwise high quality) keycaps it comes with are opaque and have a wide base which blocks out the light, also the maximum brightness is ridiculously low.

Thankfully you can buy a separate set of [translucent ABS keycaps](https://www.keychron.com/products/low-profile-abs-full-set-keycap-set?variant=40332974948441) which shine through much better, and do some QMK firmware tweaks to increase the maximum current. The default [CKLED2001_CURRENT_TUNE](https://github.com/Keychron/qmk_firmware/blob/e4f4ceaf3f2e3d25fb282273a81f9b58790fc427/keyboards/keychron/k3_pro/ansi/rgb/config.h#L104) is set to 0x14, or about 8% so I boosted it to 0x34 or about 20%.

## Keycaps

As any perfectly sane person with a lot of time would do, I sanded off the top all the keycaps by hand (ft. Dash!):

![[20230421_015107.jpg]]

![type:video](20230416_151953_3.mp4)

It looks soooooooooooooooooooooooooooooo pretty.

## Unicode

Another cool thing I did with the keyboard is implement the [agda emacs input mode](https://agda.readthedocs.io/en/latest/tools/emacs-mode.html) on the hardware itself, no pynput or AHK involved. This is extremely useful for writing proofs outside of emacs or an editor with TeX.

![type:video](20230421_025001.mp4)

It has some cool features which are not possible with built-in composition:

* Ability to type most unicode characters that are useful for programming in [Agda](https://wiki.portal.chalmers.se/agda/pmwiki.php)
* Most of the ergonomics of the emacs input mode 
* Supports Windows (WinCompose) / Mac / Linux, see QMK's [Unicode Support](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_unicode.md) docs
* OS auto-detection mostly stolen from [u/kapij](https://www.reddit.com/r/olkb/comments/x1ezbg/way_to_detect_host_os_in_qmk/) which was [merged into main](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_os_detection.md) but didn't roll into Keychron's fork until recently
* Finish query with enter key or pressing unmapped character
* Cycling with left and right arrow keys
* History with up and down arrow keys
* Print query with F1 (works with history for a reverse-query)
* Backlight hints for navigation (blue is a tree and green is a leaf)
* Sticky prefix with F2, this allows you to lock in a prefix like ^ (superscript) and holding down \\ for ¹²³⁴⁵⁶ᵃᵈᶠᵍʰʲ, or even `MI` (bold italics) 𝒕𝒐 𝒒𝒖𝒊𝒄𝒌𝒍𝒚 𝒕𝒚𝒑𝒆 𝒄𝒖𝒓𝒔𝒆𝒅 𝒕𝒆𝒙𝒕 𝒍𝒊𝒌𝒆 𝒕𝒉𝒊𝒔

This was done with over a thousand lines of [QMK](https://github.com/Keychron/qmk_firmware/tree/bluetooth_playground/keyboards/keychron/k3_pro) C code and a Dart script to convert the word to codepoint multimap to a flat tree data structure.