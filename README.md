# Fedora33-VirtualBol-with-Secure-Boot

These are the steps I did to install VirtualBox on my laptop **without disabling UEFI Secure Boot**. They're nearly similar to the process described
on [reillysiemens' git project][reillysiemensgitproject], save for a few _key_ details. The images here
comes from the [Systemtap UEFI Secure Boot Wiki][systemtap].

**My Setup:**
Laptop: Dell Inspirion 5548
SO: Fedora33
VirtualBox: 6.1.16

1. Install the VirtualBox.

1.1 Change to root.
```bash su or sudo -i ```

1.2 Install rpmrebuild Package.
dnf install rpmrebuild

1.3. Download VirtualBox-6.1-6.1.16_140961_fedora32-1.x86_64.rpm.
wget http://download.virtualbox.org/virtualbox/rpm/fedora/32/x86_64/VirtualBox-6.1-6.1.16_140961_fedora32-1.x86_64.rpm

2. 


[reillysiemensgitproject]: https://gist.github.com/reillysiemens/ac6bea1e6c7684d62f544bd79b2182a4
[systemtap]: https://sourceware.org/systemtap/wiki/SecureBoot
