taken from Aaron's page:

download links:
http://web.archive.org/web/20230127121939/https://blog.aaronhastings.me/content/buffalo_ls200/TFTP_Boot_Recovery_LS200.zip
http://web.archive.org/web/20230127121939/https://blog.aaronhastings.me/content/buffalo_ls200/nasnavi-281.zip
firmware as on buffalo page https://www.buffalotech.com/support/downloads/linkstation-200-series

https://dd00b71c8b1dfd11ad96-382cb7eb4238b9ee1c11c6780d1d2d1e.ssl.cf1.rackcdn.com/ls200-v178.zip
http://web.archive.org/web/20230127121939/https://github.com/tohenk/linkstation-mod/archive/master.zip

I recently bought a Buffalo LinkStation LS220 NAS (network-attached storage) device. The device itself is a good quality piece of hardware, but the software leaves a lot to be desired. I bought this NAS purely as a redundant (i.e. RAID1) backup solution and intended to use it as an SSH server for my rsync backup scripts. Much to my disappointment, however, the firmware came far more locked-down than I had hoped, and provided no means to (easily) enable SSH. In my struggles to find an elegant solution to this, I ended up “bricking” the device, meaning it would no longer boot. To make matters worse, I later found out that the LS220’s recovery features are stored on the disks’ /boot partition, which I had wiped while cleaning down the disks. The information available online for unbricking and/or opening the firmware of the LS200-series is… sparse. I ended up having to contact Buffalo support in order to rectify everything. Thinking they’d tell me I’d voided my warranty and couldn’t help me as such, I was pleasantly surprised at how helpful they were in providing me TFTP Boot instructions, including all the relevant software and images required. This post should act as a definitive guide to unbricking your LS200-series. I’ve even provided instructions on how to open up the firmware, enabling SSH, Telnet and more. Note that you will lose all data on your NAS, so perform any backups where possible. This guide assumes you are running Linux and that your NAS is a LinkStation LS220. Part 1: Wiping the drives

Open up the front plate on your LS220 and remove both hard drives. Take a Phillips-head screwdriver and remove the screws on both hard drive mounting plates, so that the drives come away. Attach the hard drives to your computer. You can do this using a USB-SATA hard drive (3.5″) dock (I bought this one) …or, if you have spare SATA and power cables, just connect it directly to your computer’s motherboard. Open up a terminal and run GNU Parted on the block device representing the connected hard drive. Be careful during this part, as you don’t want to wipe your computer’s primary drive. In my case, the NAS drive shows up as /dev/sdb so I run Parted as follows: $> sudo parted /dev/sdb

Using the parted print command, we can see that there are six partitions on the NAS drive by default: (parted) print Number  Start   End     Size ... 1      17.4kB  1024MB  1024MB ... 2      1024MB  6144MB  5119MB ... 3      6144MB  6144MB  394kB ... 4      6144MB  6144MB  512B ... 5      6144MB  7168MB  1024MB ... 6      7168MB  2992GB  2985GB ...

Let’s go ahead and remove all of them: (parted) rm 1 (parted) rm 2 (parted) rm 3 (parted) rm 4 (parted) rm 5 (parted) rm 6


Great! You can now unplug this hard drive and repeat the above process for the other drive. Once both drives are done, screw the mounting plates back on to the hard drives and install them back into the NAS.



Part 2: TFTP Boot This step involves flashing a minimal image to the drives, allowing it to boot into EM Mode. EM Mode allows us to get our final, fully-working firmware image on to the NAS. Unfortunately, you’ll need a Windows PC for this part. I just ran a Windows 7 VM with a network interface bridged to my host’s eth0 interface.

Connect your PC directly to your NAS with an Ethernet cable. Plug in and power on your NAS. After a few seconds, the LED on the front will flash red to let you know it failed to boot anything. The NAS will assign itself an IP address of 192.168.11.150/24. You will need to set Windows to a static IP of 192.168.11.1/24 in order to serve up the TFTP Boot image. To do this, open up Control Panel > Network and Sharing Center and click Change adapter settings. Right-click your network adapter and click Properties. Double-click Internet Protocol Version 4 (TCP/IPv4). Choose Use the following IP address and set the below values: IP address: 192.168.11.1 Subnet mask: 255.255.255.0 Default gateway: (leave blank) Then click OK and OK again to leave the Properties screen.

Download the TFTP Boot server and images from the link below. I received these from Buffalo support and am hosting them here for convenience: TFTP Boot Recovery LS200

Unzip the downloaded file and launch the TFTP Boot.exe program. The program should tell you its “listening On: 192.168.11.1:69”. If not, you have not configured your network adapter correctly. The bottom line should read “accepting requests” with a flashing cursor. Press the physical Function button on the back of your NAS until the LEDs start flashing white. The TFTP Boot window should now output two messages like below: Client 192.168.11.150 ... Blocks Served Client 192.168.11.150 ... Blocks Served

Great! At this point, your NAS will be booting a minimal image and will boot itself into EM Mode. You can close the TFTP Boot program, as we are done with it now.


Part 3: Opening up the stock firmware image (SSH, Telnet, …)

Download the NAS Navigator program from the link below. This should work in Linux under Wine/Crossover: NAS Navigator

Unzip and install the program by running NasNaviInst.exe. Now run the NAS Navigator program. After a few seconds, your NAS should show up. Note that it is in Emergency mode and has an IP address in the range 169.254.0.0/16. Repeat the instructions from Part 2, Step 3 above, but this time set an IP address of 169.254.11.1 and a subnet mask of 255.255.0.0

Very important: Note the subnet mask’s third number is a 0 (zero) and not 255. Download and unzip the latest firmware for your device from the Buffalo website below: http://www.buffalotech.com/support_and_downloads/downloads

Download and unzip the linkstation-mod tools from GitHub below: linkstation-mod  You can also clone the repo if you’re comfortable using Git.

Open up a terminal and browse to the linkstation-mod directory. Run the open-ls-rootfs.sh script on the hddrootfs.img file as below.


Bonus – root login to NAS: Add your SSH public key to the “data” directory and rename it id_rsa.key. This will automatically install the key and grant you root access to the NAS.

Very important: If you are not the root user, you must use sudo to execute the script due to some permissions requirements making /dev files. I spent hours trying to figure out why my LinkStation wouldn’t boot as a result of running this as a regular user. $> chmod +x ./scripts/open-ls-rootfs.sh $> sudo ./scripts/open-ls-rootfs.sh /path/to/firmware-directory/hddrootfs.img

After the script has completed, you will see a new directory: “out“. Inside this directory is the (hacked/opened) hddrootfs.img file. You will need to change the permissions on this back to your regular user. For example, if your username is “aaron”: $> sudo chown aaron:aaron ./out/hddrootfs.img Copy this new hddrootfs.img file to the firmware directory, overwriting the old version.

Back in Windows (sorry), open up the firmware directory and open up the configuration file LSUpdater.ini. Add the following lines to the bottom to enable Debug Mode: [SpecialFlags] Debug = 1

Run the LSUpdater.exe program. It should find your NAS. Click the window decoration in the top-left corner and click Debug(D)…Tick and untick the appropriate options until your configuration looks as below:

Click OK, then Update. You should now get a pop-up window saying “Formatting”, followed by “Transferring firmware”.

And that’s it! Your LinkStation LS200-series is now fully recovered and is now running an open firmware
