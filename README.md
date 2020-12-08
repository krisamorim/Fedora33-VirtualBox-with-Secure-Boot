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
```bash 
su or sudo -i 
```

1.2 Install rpmrebuild Package.
```bash 
dnf install rpmrebuild
```

1.3. Download VirtualBox-6.1-6.1.16_140961_fedora32-1.x86_64.rpm.
```bash
wget http://download.virtualbox.org/virtualbox/rpm/fedora/32/x86_64/VirtualBox-6.1-6.1.16_140961_fedora32-1.x86_64.rpm
```

1.4. Rebuild VirtualBox rpm with Needed Changes
```bash
rpmrebuild --change-spec-preamble='sed -e "s/6.1.16_140961_fedora32/6.1.16_140961_fedora33/"' --change-spec-requires='sed -e "s/python(abi) = 3.8/python(abi) >= 3.8/"' --package VirtualBox-6.1-6.1.16_140961_fedora32-1.x86_64.rpm
```

1.5. Install Following Dependency Packages
```bash
dnf install binutils gcc make patch libgomp glibc-headers glibc-devel kernel-headers kernel-devel dkms qt5-qtx11extras libxkbcommon
```

1.6. Install VirtualBox Latest Version 6.1 (currently 6.1.16)
```bash
dnf install ~/rpmbuild/RPMS/x86_64/VirtualBox-6.1-6.1.16_140961_fedora33-1.x86_64.rpm
```

**Note:**
This command create automatically vboxusers group and VirtualBox user must be member of that group.
This command also build needed kernel modules.

**You can rebuild kernel modules with following command if needed:**
```bash
/usr/lib/virtualbox/vboxdrv.sh setup
```



2. 


[reillysiemensgitproject]: https://gist.github.com/reillysiemens/ac6bea1e6c7684d62f544bd79b2182a4
[systemtap]: https://sourceware.org/systemtap/wiki/SecureBoot
