<h1 align="center">Nextcloud</h1>

<div align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/60/Nextcloud_Logo.svg/1280px-Nextcloud_Logo.svg.png" width="300"/>
</div>

I present you Nextcloud. An open-source cloud storage software. It use the same Dropbox system, but your file is in your cloud instead of an external server. 

My main goal was to share my files accross all my devices and everywhere. To give an example, I often scan documents with my cellphone but I hate to have to either send it to my own mail or put it in a USB stick and then plug it into my computer. With Nextcloud, I can just send it to my cloud and take it whenever I want from any devices. But what's funny is I will still use an USB stick for security purpose and backup. You see, I always wear one as a necklace, but I have to be careful because in the past, I got two USB sticks stolen with lot of personal files on them. One at work and one at the gym. Clumsy Eddy is. Anyway, I will explain to you how I installed it and the configuration needed to get it running smoothly.

## Table of contents
- Installation
  - Default settings
    - Issues encountered
  - Advanced settings


## Installation

With [Proxmox VE-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=nextcloud-vm) again, copy the line command and paste it into your server node terminal. They will ask you to either install it with the default settings or the Advanced one. When I first done it, I went with the default settings and I was expecting to use the feature *External storage*. However, I ran into some issues and had to install it manually (Advanced) 

I will explain the installation with the default settings and what issue I ran into, then I will get to how I installed it and the configuration done.

### Default settings
- Machine type: i440fx
- Disk size: 12GB
- CPU Cores: 2
- RAM size: 2048MB
- MTU Size: Default

The default settings will configure the hardware need for a ready-to-use. Pretty simple and easy to do. You just gotta wait for it to install your VM then you can click on your VM and continue the installation within the Shell. 

In your Shell, that's the step of the installation where you'll install Turnkey Linux, a Debian/GNU Linux based library that give you a ready-to-run Nextcloud foundation. In the Shell, you will have options for partinoning. I recommend to go with the *Guided - use entire disk and set up LVM*. Why? Because I saw a tutorial doing it and it went smooth. 

After clicking on Enter, they will ask you on which disk to install it. Why is there two disks you migh ask? That's because of the line command. In the installation of your VM, it will create to harddisk inside your VM. One is for the tunrkey-root (sda) which is the database of your nextcloud and the other one, I believe is the Nextcloud installation media (sdb). So make sure to select sda. Next page, click yes.

> [!TIP]
> Like I always say, read everything to understand what you're doing. It is better to understand the process instead of just following the steps blindly.

Next page is if you wish to use the whole volume groupe for partitioning. Depending on if you wish to add more disk later, you can leave it at 90%. However, if you don't plan on adding more disk, you can set it to *max*. Before going further, keep in mind that the default settings only gives 12GB of storage. So I suggest you to not put it to *max* in case you need to make some changes. 

Next page, click *yes* to write changes to disks. Wait for the installation and click *yes* to install the GRUB boot loader. Otherwise, the disk used for Nextcloud won't boot. After the processus, it will prompt you to Reboot. Do it and when you get to the black screen with Turnkey on top, Stop your VM. Go to hardware and detach then remove your second hard disk (828M) so your VM can boot with the one you just installed Nextcloud on (sda). 

When that's done, restart your VM with the Console. Wait for it to load. You will then get into the configuration of your login information. Follow the steps and make sure to write down your login informations in case you forget.

At some point, it will ask you to write the Nextcloud domain. You have to write the IP address of your VM (for some, they might already have a domain configured, but I believe it won't be the case for everyone). You can find your IP address in the *Summary*.

After you put it in, you will have a window asking you to input an API key. If you don't have one, just skip that part. Next one is gonna ask you an email. That's up to you. Finally, they will ask you if you wish to install the *Security updates*... Just do it, it will be done right away. When that's done, Reboot by clicking enter in the prompt.

After the reboot, you will have a window with all the IP address and SSH configuration. Just quit and it will get you to the terminal. Put your login information.

> [!TIP]
> In this [link](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/), it will help you with the Post-Installation. All the steps required to get your Nextcloud ready to go such as creating an user for SSH connexion, how to update and how to fix some errors. In the link, they will also recommend a SSH client called Termius. That's what I'm using and I really like it.

#### Issues encountered

I will explain to you the issues that I encountered when I used the default settings for installation. 

After the installation, if you followed the default settings, you'll have 8.10GB in storage, but 3.30GB will already be used for Nextcloud, so you're left with 4.81GB available. That's not a lot. 

So, my plan was to use the *External Storage* feature and add my SSD that was plugged into my TP-Link router. I wanted to use the USB Sharing feature from the router. Within the *Externnal Storage*, the configuration through FTP went smoothly. I was able to add the SSD in Nextcloud and use the storage. However, what I was not expecting is that my SSD would often disconnect from Nextcloud. Because of that, I couldn't use my SSD and the synchronization from my other devices would be constantly stopped thus alway givine me a notification of error. 

I thought to myself, that's not the end of the world. I can still try to plug it into my Thinkpad and mount it to the VM. After mounting it, I thought I was finally done, but lucky for me, I ran into the same problem of disconnection. 

At this point, thinking it was a problem with the enclosure used, I just uninstalled everything, stopped using the USB Share feature from TP-Link and reinstalled with the Advanced settings. 

So instead of having +1TB of storage, I could only allocate 100-120GB for the VM because my local-lvm only has 148.77GB available if I don't count the Nextcloud VM. So 100-120GB would be enough for more files and apps in Nextcloud, but that will put a limitation on how many containers or VMs I could install on my server. For now, it does the job as I only have 50GB worth of files, but later on, I will have to find a new solution. I am scared that I might have to go from zero: upgrade the disk on my ThinkPad and reinstall my Proxmox server on the new disk. So basically reinstalling Adguard Home, Tailscale and Nextcloud.

*Still in work*
