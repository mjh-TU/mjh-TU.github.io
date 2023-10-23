Download ISO-
    Downloaded ISO from Arizona edu mirroring website, and checked the SHA256 hash with the hash on archlinux website and they match
Boot VM from ISO-
    Start Install process

    Once I was on the terminal, I could't type, then I realized I needed more RAM and not the 768 MB and then when I rebooted the machine finally booted up correctly

Configuring
    Found US keyboard layout using the command
        ls -l /usr/share/kdb/keymaps/**/*.map.gz | grep -i /qwerty/us

    Set Keyboard Layout
        loadkeys us.map.gz

    Did not set console font

    Ran the command
        cat /sys/firmware/efi/fw_platform_size
    
    That directory efi did not exist so, I searched online and that means I was in bios boot mode, so I went to the VM settings and changed it to boot to UEFI

    Ran the command again
        cat /sys/firmware/efi/fw_platform_size

    The command outputed 64 which means I am in 64 bit mode which is what I need to be in

    Connect to Internet
        run command to check if IP assinged
            ip a
        ran a ping to google and have internet
        Internet was already pre-configured
    
    Update system clock
        Run the command
            timedatectl
        Time is in UTC and the time is correct

    Partition Disks
        run the command
            fdisk -l
        the loop0 can be ignored so the disk going to be used is /dev/sda

        run command: fdisk /dev/sda
        type m for more info

