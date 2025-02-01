### General

- Boot using Dynamic GPU Mode

- Extract internal monitor EDID using Nvidia settings app

- Put the extracted `edid.bin` into `/usr/lib/firmware/edid/edid.bin`

- Include `edid.bin` into initial ramdisk

- Add `drm.edid_firmware={Your display id}:edid/edid.bin` into linux kernel cmd line params

### Fedora

For anyone trying to fix this on Fedora (Tested on Fedora 36):

- Boot using Dynamic GPU Mode

- Find out and remember **display id** of internal display. `ls /sys/class/drm` will list graphics cards and the ports they have: 

```
card0 card0-eDP-1
``` 
**eDP-1** is **display id** in this case

- Boot using Discrete GPU Mode

- Using nvidia-settings, extract EDID of internal display

- Copy edit.bin to /usr/lib/firmware/edid/ 

```
sudo cp (path to edit.bin) /usr/lib/firmware/edid/eDP.bin
```

- Enable **TearFree** and **VariableRefresh** rate (Also set **DRI** to 3) by adding params to Xorg config: /etc/X11/xorg.conf.d/20-amdgpu.conf

```
Section "Device"
     Identifier "AMD"
     Driver "amdgpu"
     Option "VariableRefresh" "true"
     Option "TearFree" "1"
     Option "DRI" "3"
EndSection
```

- Add custom edid to **grub command line** using **grubby**

```
sudo grubby --args="drm.edid_firmware={Your display id}:edid/eDP.bin" --update-kernel /boot/vmlinuz-$(uname -r)
```

- Add firmware file to **initramfs** by using **dracut** config files. Create **/etc/dracut.conf.d/99-local.conf** file with contents:

```
install_items+=" /usr/lib/firmware/edid/eDP.bin "
```

- Regenerate **inintramfs** with 

```
dracut -f
```

- Reboot

Now `xrandr --query` outputs
```
Screen 0: minimum 320 x 200, current 3600 x 1600, maximum 16384 x 16384
eDP connected primary 1680x1050+0+0 (normal left inverted right x axis y axis) 345mm x 215mm
   2560x1600    165.02 +  60.01 + 120.04    96.01    72.01    60.01  
   1920x1200    165.02  
   1920x1080    165.02  
   1600x1200    165.02  
   1680x1050    165.02* 
   1280x1024    165.02  
   1440x900     165.02  
   1280x800     165.02  
   1280x720     165.02  
   1024x768     165.02  
   800x600      165.02  
   640x480      165.02
```
