# Tuxedo Infinitybook 14v6 setup
This repository is to help those who bought the Tuxedocomputers infinitybook 14v6 and have experienced the same problems as me. These are the steps and fixes that I applied myself, if --- by some cosmic random event --- your machine is bricked because of this, don't come crying to me about it. Do your own research before you just paste random code from the internet on your machine.

## Serious f*%!ed up graphics
So you just installed your favorite Arch derivative (for me that was Garuda), but something is wrong. When you move your mouse the screen flickeres becoming full black, some graphical glitches appear at random, sometime the whole screen goes to one colour. This was the most frustrating thing for me. I simply could not use the system. I was extremly close to just keep the ubuntu installation and deal with the complications that come with it. However, after some digging in the arch user forum I found a solution (although a bit wierd). The intel power management implementation in linux is absolut shit. I had to uninstall the ``xf86-video-intel`` package and pass the following kernel parameters, before my system behaved "normaly"
```
i915.modeset=1 i915.enable_dc=0 i915.enable_fbc=0 i915.enable_psr=0 intel_idle.max_cstate=1
```
These arguments are passed in the grub start menu, where you select your boot method (often 2 with one being free/open source drivers and the other being nvidia) press "e" and paste this in the line starting with "boot", you must pass the arguments just before the "modeset's" and "quiet splash" appear (if they don't just research it yourself).

To make this persistent you need to pass this to the grub config, please do your [own research](https://wiki.archlinux.org/title/GRUB) as this is outside the scope of this guide.

## 5 GHz signal not working
This must be the next thing I have had some serious problems with. My private network is configured to have both 2.4GHz and 5GHz, however my universities network is mostly 5Ghz with 2.4GHz being horse crap. I were completly frustrated that my _brand new computer_ *COULD NOT EVEN CONNECT TO MY NETWORK PROPERLY*. I were about 5 seconds to sending it back to Germany and having them taking a look, when I found [this page](https://www.intel.com/content/www/us/en/support/articles/000005511/wireless.html) from intel. For some wierd reason, the driver I needed was not shipped with any linux distribution I could get my hands on. The infinitybook 14 v6 uses the Intel® Wi-Fi 6 AX200 160MHz card, and even supports bluetooth. 

The solution to the problem is simply downloading the driver ``iwlwifi-cc-46.3cfab8da.0.tgz`` and copy the ``.ucode`` file in to the ``/lib/firmware/`` folder and it just works.

## Keyboard lights and Tuxedo Control Center
Tuxedo Computers have opensourced a lot of their code for their own software tailored to their computers. Take a look at their [github group](https://github.com/tuxedocomputers). For arch derivites you can install packages [``tuxedo-control-center``](https://aur.archlinux.org/packages/tuxedo-control-center/)<sup>aur</sup> and [``tuxedo-keyboard``](https://aur.archlinux.org/packages/tuxedo-keyboard/)<sup>aur</sup>.

## Power management
For some reason the inefficient ``s2idle`` suspend variant is selected by default. One should choose the more power effecient ``deep`` variant. The following shows an example of how to read the current power management profile.
```bash
$ cat /sys/power/mem_sleep
[s2idle] deep
```
The selected is put in square brackets.

To tempoaryily change to ``deep`` run the following
```bash
echo deep | tee /sys/power/mem_sleep
```
Add the following to your kernel parameters to make this change permanent 
```
mem_sleep_default=deep
```