# Enabling_PK3C-TCP
A how-to on how to enable TCP-PK3C on your Linux Server, an alternative congestion control that promises to be better than Google's BBR and (Linux default) CUBIC.

---

<b>INTRODUCTION</b>
This guide focuses on how to compile and load the congestion control algorithm PK3C as a kernel module.
Tested on Debian 10
Read more about PK3C here: https://github.com/Ludww/pk3c

---

<b>CONFIGURATION</b>

Tested on Debian 10 default kernel version 4.19x, according to the author(s) it should work normally on later kernel versions

---

<b>DEBIAN 10</b>

Install Linux headers, build essential and git

    sudo apt install linux-headers-`uname -r` build-essential git

Get PK3C from github

    cd /usr/src && sudo git clone https://github.com/Ludww/pk3c && cd pk3c

Compile the module

    sudo make -C "/usr/src/linux-headers-`uname -r`" M=/usr/src/pk3c modules

Load module into the Kernel to test

    sudo insmod /usr/src/ltcp_pk3c.ko

Check if module is loaded correctly

    sudo lsmod | grep pk3c

If the module was loaded correctly, make the changes persistent

    sudo nano /etc/sysctl.conf

Add this line at the end of the file, save and exit

    net.ipv4.tcp_congestion_control=pk3c

Apply the new configuration

    sudo sysctl -p

Copy module file into system

    sudo cp /usr/src/pk3c/tcp_pk3c.ko /lib/modules/`uname -r`

Add PK3C to startup modules list

    sudo nano /etc/modules

    Add tcp_pk3c to the end of the file, save and exit
    tcp_pk3c

Run Depmod

    sudo depmod

Restart the system

    sudo shutdown -r now

And check if the module was loaded correctly on startup with

    sudo cat /proc/sys/net/ipv4/tcp_congestion_control
    
If you see PK3C listed, that's it.

---

<b>CONCLUSION</b>

Variety in the ecosystem is always a good thing, the addition of this new (and as of the time of this writing, actively maintained) congestion control to the list brings more options for more use-cases, improving the overall quality of Linux network ecosystem.

---

<b>REFERENCES</b>

PK3C Github - https://github.com/Ludww/pk3c</br>
Other congestion control algorithms - https://en.wikipedia.org/wiki/TCP_congestion_control</br>
