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
    - Apps/Features installed
- Conclusion


## Installation

With [Proxmox VE-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=nextcloud-vm) again, copy the line command and paste it into your server node terminal. They will ask you to either install it with the default settings or the Advanced one. When I first done it, I went with the default settings and I was expecting to use the feature *External storage*. However, I ran into some issues and had to install it manually (Advanced) 

I will explain the installation with the default settings and what issue I ran into, then I will get to how I installed it and the configuration done.

### Default settings
- Machine type: i440fx
- Disk size: 12GB
- CPU Cores: 2
- RAM size: 2048MB
- MTU Size: Default

The default settings will configure the hardware needs for a ready-to-use. Pretty simple and easy to do. You just gotta wait for it to install your VM then you can click on your VM and continue the installation within the Shell. 

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
> Follow this [link](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/), it will help you with the Post-Installation. All the steps required to get your Nextcloud ready to go such as creating an user for SSH connexion (you can still use the *admin* user if you wish to), how to update and how to fix some errors. In the link, they will also recommend a SSH client called Termius. That's what I'm using and I do recommend it. Just know I didn't look for other clients.

> [!NOTE]
> I forgot to say that in the [Nextcloud Post-Install Guide](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/), when trying to change the php.ini file, you might not have the same path as him, so it is a possibility that you encounter an error saying *There's no such file*. To resolve this issue, I had to search online and found out that by changing <ins>fpm</ins> to <ins>apache2</ins> in the line command, I was able to get into the file and make the necessary change.

> [!TIP]
> In the Administrator settings of your Nextcloud server, the [Nextcloud Post-Install Guide](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/) won't tell you how to resolve the error about the *App API daemon error*. To resolve this issue, I had to look again online and found in some reddit threads that you just have to disable an app in your app library. To do so, click on your profile and go to **Apps**. In the **Active apps** section, disable the following: AppAPI. Then go back to your Administration settings and the error should not appear in the **Security & setup warnings**.

#### Issues encountered

I will explain to you the issues that I encountered when I used the default settings for installation. 

After the installation, if you followed the default settings, you'll have 8.10GB in storage, but 3.30GB will already be used for Nextcloud, so you're left with 4.81GB available. That's not a lot. 

So, my plan was to use the *External Storage* feature and add my SSD that was plugged into my TP-Link router. I wanted to use the USB Sharing feature from the router. Within the *Externnal Storage*, the configuration through FTP went smoothly. I was able to add the SSD in Nextcloud and use the storage. However, what I was not expecting is that my SSD would often disconnect from Nextcloud. Because of that, I couldn't use my SSD and the synchronization from my other devices would be constantly stopped thus alway givine me a notification of error. 

I thought to myself, that's not the end of the world. I can still try to plug it into my Thinkpad and mount it to the VM. After mounting it, I thought I was finally done, but lucky for me, I ran into the same problem of disconnection. 

At this point, thinking it was a problem with the enclosure used, I just uninstalled everything, stopped using the USB Share feature from TP-Link and reinstalled with the Advanced settings. 

So instead of having +1TB of storage, I could only allocate 100-120GB for the VM because my local-lvm only has 148.77GB available if I don't count the Nextcloud VM. So 100-120GB would be enough for more files and apps in Nextcloud, but that will put a limitation on how many containers or VMs I could install on my server. For now, it does the job as I only have 50GB worth of files, but later on, I will have to find a new solution. I am scared that I might have to go from zero: upgrade the disk on my ThinkPad and reinstall my Proxmox server on the new disk. So basically reinstalling Adguard Home, Tailscale and Nextcloud.

### Advanced settings
- Machine type: i440fx
- Disk size: 115GB
- CPU Cores: 2
- RAM size: 4096MB
- MTU Size: Default

Like I said before, because I ran through some issues and wasn't able to find a solution (even if there might've been some for my situation), I just uninstalled the VM and installed it again. However, I chose Advanced settings because of some modifications I wanted to do
- Storage disk: For this one, I allocated 115GB of disk for my VM. This storage will be used by Nextcloud for its data. So far, I've used 65.32GB on a size of 97.38GB.
- RAM size: The default settings will only allocate 2GB of RAM. However, I wanted to give 2 more GB because I've seen a high usage, close to the maximum when updating. Plus, I also wished to add more Apps and Integrations so I needed more to make sure they would run smoothly altogether. As of the moment, the VM uses 1.55GB to 1.60GB in continuous running.

So, for the installation, it's still pretty simple and quick. Just follow the prompts and input the desired amount/informations. Usually, the installation will still input the default settings, so I just had to change the two hardware mentioned before.

When the installation process of the VM is done, I just had to follow the same steps as mentioned above in the default settings installation process. Also, I've followed the [Nextcloud Post-Install Guide](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/) from Hake Hardware. 

> [!NOTE]
> Again, in the [Nextcloud Post-Install Guide](https://wiki.hakedev.com/docs/proxmox/nextcloud-post-install/), when trying to change the php.ini file, you might not have the same path as him, so it is a possibility that you encounter an error saying *There's no such file*. To resolve this issue, I had to search online and found out that by changing <ins>fpm</ins> to <ins>apache2</ins> in the line command, I was able to get into the file and make the necessary change. 

#### Apps and Features installed

When you click on your profile, in the **Apps** section, you can find plenty of apps and integrations to add in your Nextcloud server. Some of them can be very practical depending on why and how you setup your server. In my case, I added the following:

- External storage support: I still added this feature. The reason was to put my Music library on a USB key and access to it via the *TP-Link USB Sharing* feature on my router. This way, it doesn't take space on my main storage. However, I still get some disconnection issues like mentioned before in the *Default settings installation*
  
- Calendar: I just like to have all my appointments, tasks and important dates on all my devices. There's an option to import calendar from any applications by uploading a **.ics, .ical, .ifb, .icalendar** file. Note that it won't synchronize with your uploaded calendar, so if you ever add new events on your main calendar application, you have to make sure to also add it in your Nextcloud
  
- Audio Player: You can use it to play any audio files through your Nextcloud server. In my case, I only use it to play audio files that aren't Songs since I will add an another App so they aren't mixed up together
  
- Automated media conversion: It can automatically convert media when they're uploaded to a formats supported by FFmpeg
  
- Camera RAW Previews: It makes the preview of **RAW** files from cameras available in your Nextcloud
  
- Deepl Translation: With an API key, I can make my server use Deepl translations services. It can also be integrated with Nextcloud Assistant, but for now, I don't plan on using the Assistant
  
- G DATA Antivirus: It adds an additional layer of security for my server. It can scan files for malicious content
  
- LibreSign: I can sign PDF using digital certificates if I wish to
  
- Maps: Provides a map in your server with different map style (Standard, Satellite,Topographical, etc). It can scan your Photos folder and show where they were taken (if location was enabled when taking the picture). You can also track your devices and track trips you did
  
- Memories: Photo management app with tons of features
  
- Music: A stand-alone music player app. Comes with a lot of nice features and you can even play internet raido and podcasts channels. I use that one to have all my songs instead of the Audio Player
  
- ONLYOFFICE: I can create, edit and co-author text documents, spreadsheets, presentations and PDF. Comes with a lot of features as well
  
- PDF Viewer: Just like the name implied. I added it to make sure I can view my PDF files
  
- Passwords: A password manager for Nextcloud to store my password in my own cloud. I use it in tandem with Bitwarden, which I recommend. I used 1Password in the past but changed to Bitwarden . To be honest, both are great manager, even I don't remember why I switched. There's also an extension browser available and applications for bot IOS and Android phones.
  
## Conclusion

So that's it for my installation and configuration of my own cloud with Nextcloud. To be honest, it's a lifesaver because of the fact that I don't have to pay for a subscription to have more storage from Dropbox or other cloud services applications. Also, with all the apps and integrations you can put, it's really worth adding in your server. 
