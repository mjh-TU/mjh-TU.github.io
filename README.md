# mjh-TU.github.io

# Arch Linux Install

### Download ISO-
    Downloaded ISO from Arizona edu mirroring website, and checked the SHA256 hash with the hash on archlinux website and they match
### Boot VM from ISO-
    Start Install process

    Once I was on the terminal, I could't type, then I realized I needed more RAM and not the 768 MB and then when I rebooted the machine finally booted up correctly

### Configuring
    Found US keyboard layout using the command
        ls -l /usr/share/kdb/keymaps/**/*.map.gz | grep -i /qwerty/us

### Set Keyboard Layout
    loadkeys us.map.gz
    Did not set console font
    
###  Verify Boot mode
    cat /sys/firmware/efi/fw_platform_size
    That directory efi did not exist so, I searched online and that means I was in bios boot mode, so I went to the VM settings and changed it to boot to UEFI

    Ran the command again
        cat /sys/firmware/efi/fw_platform_size

    The command outputed 64 which means I am in 64 bit mode which is what I need to be in

 ### Connect to Internet
        run command to check if IP assinged
            ip a
        ran a ping to google and have internet
        Internet was already pre-configured
    
 ### Update system clock
        Run the command
            timedatectl
        Time is in UTC and the time is correct

### Partition Disks
        run the command
            fdisk -l
        the loop0 is the iso file

        run command: fdisk /dev/sda
        type m for more info
        typed n
        select primary
        use default partition number
        use default first sector of 2048
        then type +500M to have 500 MiB for the UEFI

        type n for new partition
        select primary
        then use the default settings for the rest of the disk

        Finally use w to write to disk
    
 ### Format disks
        I chose sda1 for the efi system and sda2 for the primary
        to format the efi system partition
            mkfs.fat -F 32 /dev/sda1
        To format the primary to ext4
            mkfs.ext4 /dev/sda2

 ### Mounting the file systems
        mount the primary root partition to /mnt
            mount /dev/sda2 /mnt
        Then mount the efi system partition
            mount --mkdir /dev/sda1 /mnt/boot
 ### Make user
        Then I created a new user 'archuser', Note: there is no sudo group so it is a normal user
            command- useradd archuser
        Then I changed the password for the user:
            passwd archuser
        Password is: 
            password
        I then deleted the user and forgot to document why (added this comment after rebooting the machine and was unable to log in)

 ### Install essential packages
        run the command:
            pacstrap -K /mnt base linux linux-firmware

 ### Configure the system:
        generate an fstab file
            genfstab -U /mnt >> /mnt/etc/fstab
        change root into the new system
            arch-chroot /mnt
        Set time zone
            ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
            hwclock --systohc
    to install packages use pacman
        pacman -S vim
 ### Localization:
        edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8
            vim /etc/locale.gen
        generate the locales by running
            locale-gen
        create the locale.conf file
            touch /etc/locale.conf
            vim /etc/locale.conf
            add this: LANG=en_US.UTF-8
        set console keyboard layout
            vim /etc/vconsole.conf
            add: KEYPMAP=
            Leave it blank as it will default to us keyboard
        Set hostname
            vim /etc/hostname
            type in what you want your hostname to be, I did: archlinuxmachine
 ### initramfs
        run command: mkinitcpio -P
 ### Root Password:
        passwd
        I set password to 'password'
    
 ### I then did another snapshot just in case

 ### Installed other packages
        pacman -S networkmanager
        pacman -S iwd
        pacman -S man-db
        pacman -S man-pages
        pacman -S texinfo
    
        Installed these as well
            pacman -S amd-ucode
            pacman -S grub
            pacman -S efibootmgr
    
 ### Bootloader
        the mounting point for the efi is /mnt/boot

        ran the command: 
            grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB

        The directory /mnt/boot diddnt exist and nothing in /mnt so I reran the mount commands 
        from the previous section and ran the command again and it worked:
            mount /dev/sda2 /mnt
            mount --mkdir /dev/sda1 /mnt/boot
        
        Generate the main configuration file
            grub-mkconfig -o /boot/grub/grub.cfg
    
 ### Performed another snapshot before exiting chroot and rebooting

    Once I rebooted, i was unable to log in as I forgot I have deleted the archuser account and by defaut cant log in as root. So i reverted back to a snapshot and created the user:
        useradd archuser
        passwd archuser
        password is: password
    
 ### Install and configure sudo
        pacman -S sudo
    
        added user to wheel group
            usermod -aG wheel archuser

        Switched user and it said user was not in sudoers file, so I edited the sudoers file by uncommenting a line which gives any user in that group sudo access: %wheel ALL=(ALL:ALL) ALL

        Tried the command:
            sudo cat /etc/shadow
            the sudo worked so i can now reboot
    
        Was successfully able to log in as the archuser account
    
 ### Create the codi account with sudo privileges
        sudo useradd codi
        sudo usermod -aG wheel codi
    
 ### Had no internet, so I restarted the networkmanager, and also enabled the service using:
        sudo systemctl restart NetworkManager
        sudo systemctl enable NetworkManager
    
 ### Install fish shell
        sudo pacman -S fish
    
 ### Install openssh
        sudo pacman -S openssh
    
 ### Useradd did not create home directory so i ran the command
        sudo mkhomedir_helper archuser

 ### Make backup of the .bashrc systemwide
        sudo cp /etc/bash.bashrc /etc/bash.bashrc.backup

 ### Customize
        Change colors of the terminal by adding these lines to the /etc/bash.bashrc (makes new users have a color scheme, for current user add it to the .bashrc file in the users home directory):
        export PS1='\e[33;1m\u@\h: \e[31m\W\e[0m\$ '
        export LS_COLORS='rs=0:di=01;34:ln=01;36:mh=00:pi=40;33'

    

 ### Install GUI:

        Going to use Budgie

        Install budgie and xinit:
            sudo pacman -S budgie
            sudo pacman -S xorg-xinit
        
        Use the xinitrc and configure it to include budgie
            sudo cp -r /etc/X11/xinit/xinitrc.d ~/.xinitrc

            Then add the lines at the end of the file
                export XDG_CURRENT_DESKTOP=Budgie:GNOME
                exec budgie-desktop

        Going to try this way instead as couldnt figure out using arch wiki:
            install xorg
                sudo pacman -S xorg xterm xorg-xinit
            Command to start desktop enviornment:
                startx
            configure to boot when the user logs in:
                Add the lines into the ~/.bash_profile:
                    if [ -z "$DISPLAY" ] && [ "$XDG_VTNR" -eq 1 ]; then
                        exec startx
                    fi
        
        Create alias:
            alias startyprint='cat ~/.bashrc'
            alias startypath='/home/archuser/.bashrc'

    

    







    
        

    
    

    
