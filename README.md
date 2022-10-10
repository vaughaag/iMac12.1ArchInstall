# iMac 12,1 Arch Linux Install 

![](/images/Screenshot.png)

### Created at the request from Linux on Mac FB Group. Although this guide is for the iMac 12,1, it should work for all 12x and 13x iMacs as the hardware/drivers are similar generations.
---

### This guide will use the Arch Linux install script now bundled with Arch as it is far easier to use with little need to erro/fault find. If comfortable installing Arch without thi tool, skip to step XX
---
```diff
+ An ethernet connection will be required during the process as the iMac's wireless does not always work using iwctl, especially with recent releases. If you do not have access to an ethernet connection, check the wiki page linked just below to try and get wireless working.
````
https://wiki.archlinux.org/title/iwd 

```diff
- Make sure all your important data is backed up before continuing. Your entire drive will be wiped following this guide. If you wish to dual boot, configure the drive and disk layout options according to your own configuration. If you are unsure, DO NOT PROCEED. 
````

---
## Index
#### [Step One - Download, create USB and run installer ](#step-one)
#### [Step Two](#step-two---install-drivers-and-apps)
#### [Install git](#git-1)
#### [Install AUR Helper](#yay-aur-helper)
#### [Fan Control](#macbook-fan---this-aids-in-controlling-the-fans-and-works-on-macbook-and-imac)
#### [Light, screen brightness control](#light---allows-you-to-control-the-screen-brightness-from-the-terminal)
#### [Bluetooth](#bluetooth-1)
#### [Wireless](#wifi---a-restart-will-be-required-after-this-step)
#### [Nerd Fonts](#nerd-font-pack)
#### [Alacritty Config](#alacritty-config)

----

## Step One
* Download the latest Arch ISO - https://archlinux.org/download/ 
* Download Etcher to write the ISO to a USB - https://www.balena.io/etcher/ 

A USB flash drive of 4GB will be required. Once the ISO has downloaded and Etcher is installed, open Etcher and select the ISO image, select usb drive and click flash.

![](/images/etcher.png)

Once complete, plug the drive into the iMac and power it on, press and hold the Option key (⌥) as soon as you hear the boot chime or when the screen initially goes grey. This will load a device selection, there should be an orange drive on the right hand side called EFI. Select this one and hit enter.

The installer will now load and you should see the following prompt
![](/images/archprompt.png)

Type ip a and hit enter and make sure you have an ip address. Simply now type 'archinstall' and hit enter. If an error is generated, referencing key databases, enter the following one line at a time

    killapp gpg-agent
    pacman-key --init
    pacman-key --populate archlinux

Then type archinstall again and hit enter. The installer script should load and look like this

![](/images/archinstall.png)

You can now work through the options. I would suggest selcting world wide within the mirror list for now. This can be changed later once Arch is installed.

For the bootloader, I would suggest Grub as systemd boot can be problematic, especially if you are dual booting with macOS or another operating system. 

Create a root password and then a user account. Make sure to 'Confirm and Exit' after creating the user account, otherwise once the install has completed the root accoutn will be the only account setup.

Set the profile to Minimul and audio to Pipewire

For the Kernel, select either 'linux' or 'linux-lts' at the time of writing the zen and hardened kernels resulted in boot loops.

in the additional packages enter the following making sure a space if between each package name.

    xorg sddm plasma plackagekit-qt5 nano alacritty dolphin base-devel networkmanager firefox

This will install a base using KDE Plasma desktop environment, additional packages can be added here or later and further packages for hardware and function support are added below.

Use the down arrow to select Install and kit enter.

Once the install has completed, select No and then type reboot and hit enter.

You should be greated by the Grub Bootlader, select *Arch Linux and hit enter or let the countdown finish and Arch will boot.

![](/images/grub.png)


At the login prompt enter the credentials for the user account you created. And once logged in type the following.

    sudo systemctl enable sddm

Then reboot the system 
    
    reboot

This time, you should see a graphical login screen. 

Once logged in open Alacritty by clicking the app launcher in the bottom left corner and typing alacritty.

Update the system by typing the following

    sudo pacman -Syu

# Step Two - Install Drivers and Apps

## GIT

In alacritty type the following, you may be promted for you password severl times

    sudo pacman -S git -y

Select Y or yes for any dependancies reauired

## YAY Aur Helper

    git clone https://aur.archlinux.org/yay-git.git
    cd yay-git
    makepkg -si

## Macbook Fan - This aids in controlling the fans and works on Macbook and iMac

    yay -S mbpfan-git
    
Follow the instructions on screen, selection Yes. Once installed enable and start the service.

    sudo systemctl enable mbpfan.service
    sudo systemctl start mbpfan.service

The service comes complete with a standard config file. This is located /etc/mbpfan.conf I have included an edited config for an iMac 12,1 

````diff
! at times, the service will stop working and the fan's, mainly the hard drive fan will spin up. You can reset the service by typing sudo systemctl restart mbpfan.service 
````
mbpfan.conf
    
    [general]
    # see https://ineed.coffee/3838/a-beginners-tutorial-for-mbpfan-under-ubuntu for the values
    # 
    # mbpfan will load the max / min speed of from the files produced by the applesmc driver. If these files are not found it will set all fans to the default of min_speed = 2000 and max_speed = 6200
    # by setting the values for the speeds in this config it will override whatever it finds in:
    # /sys/devices/platform/applesmc.768/fan*_min
    # /sys/devices/platform/applesmc.768/fan*_max
    # or the defaults.
    #
    # multiple fans can be configured by using the config key of min_fan*_speed and max_fan*_speed
    # the number used will correlate to the file number of the fan in the applesmc driver that are used to control the fan speed.
    #
    #min_fan1_speed = 2000	# put the *lowest* value of "cat /sys/devices/platform/applesmc.768/fan*_min"
    #max_fan1_speed = 6200	# put the *highest* value of "cat /sys/devices/platform/applesmc.768/fan*_max"

    # temperature units in celcius
    [general]
    min_fan1_speed = 1400
    max_fan1_speed = 4300
    min_fan2_speed = 1100
    max_fan2_speed = 6200
    mix_fan3_speed = 1200
    max_fan3_speed = 2600
    low_temp = 35			# if temperature is below this, fans will run at minimum speed
    high_temp = 46			# if temperature is above this, fan speed will gradually increase
    max_temp = 85			# if temperature is above this, fans will run at maximum speed
    polling_interval = 1	# default is 1 seconds
 

## Light - Allows you to control the screen brightness from the terminal

    yay -S light-git

Once installed, it is active right away. You can set/control the screen brightness with the following commands. This should be retained followed shutdown/reboot.

Set at 75% (change the percentage as required)

    light -S 75

Increase by %

    light -A 10

Decrease by %

    light -U 10


## Bluetooth

In alacritty enter, line at a time

    sudo pacman -S bluez bluez-utils
    sudo systemctl enable bluetooth
    suto systemctl start bluetooth

## Wifi - A restart will be required after this step

In alacritty enter, line at a time

    yay -S broadcom-wl
    sudo systemctl enable networkmanager
    sudo systemctl start networkmanager

    reboot

## Nerd Font Pack 

In alacritty enter, line at a time

    git clone https://aur.aurchlinux.org/ner-fonts-hack-git
    cd nerd-fonts-hack
    makepkg -si

## My alacritty config, inc colours and font setup

    git clone https://github.com/vaughaag/Dotfiles
    mkdir .config/alacritty
    mv Dotfiles/alacritty/alacritty.yml .config/alacritty

Close and reopen alacritty and the config should take effect. 