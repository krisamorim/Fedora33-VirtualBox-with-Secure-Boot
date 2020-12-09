# Fedora33-VirtualBol-with-Secure-Boot

These are the steps I did to install VirtualBox on my laptop **without disabling UEFI Secure Boot**. They're nearly similar to the process described
on [reillysiemens' git project][reillysiemensgitproject], save for a few _key_ details. The images here
comes from the [Systemtap UEFI Secure Boot Wiki][systemtap].

**My Setup:**
Laptop: Dell Inspirion 5548
SO: Fedora33
VirtualBox: 6.1.16

**1. Install the VirtualBox.**

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

1.7. Add VirtualBox User(s) to vboxusers Group
```bash
usermod -a -G vboxusers userName
```
Replace userName with your own user name or some another real user name.

**2. Sign virtual box modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci)**

2.1. Install mokutil package
```bash
sudo dnf update
sudo dnf install mokutil
```

2.2. Create RSA key under new folder.
```bash
sudo -i
mkdir /root/signed-modules
cd /root/signed-modules
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=VirtualBox/"
chmod 600 MOK.priv
```

2.3. This command will ask you to add a password, you need this password after the next reboot.
```bash
sudo mokutil --import MOK.der
```

2.4. Reboot your system and a blue screen appear:
 
   - Select Enroll MOK

    ![Enroll MOK][enroll mok]

   - after select continue 

    ![Continue][continue]

   - Select _Yes_ to enroll the keys.

   ![Confirm][confirm]

   - Enter the password from earlier.

   ![Enter password][password]

   - Select _OK_ to reboot.

   ![Reboot][reboot]

 after put the previous password and hit enter.

2.5 Put the commands below in a script to run it later (after system update)
```bash
cd /root/signed-modules
vim sign-virtual-box
Add the following cmd to this script :

#!/bin/bash

for modfile in $(dirname $(modinfo -n vboxdrv))/*.ko; do
  echo "Signing $modfile"
  /usr/src/kernels/$(uname -r)/scripts/sign-file sha256 \
                                /root/signed-modules/MOK.priv \
                                /root/signed-modules/MOK.der "$modfile"
done
```

2.6. Add exec permission and run the script
```bash
chmod 700 sign-virtual-box
./sign-virtual-box 
```

6- Launch VirtualBox
```bash
modprobe vboxdrv
```



[reillysiemensgitproject]: https://gist.github.com/reillysiemens/ac6bea1e6c7684d62f544bd79b2182a4
[systemtap]: https://sourceware.org/systemtap/wiki/SecureBootandando
[enroll mok]: https://sourceware.org/systemtap/wiki/SecureBoot?action=AttachFile&do=get&target=Screenshot_kvm-rawhide-64-uefi-1_2014-02-27_14_00_13_crop.png
[continue]: https://sourceware.org/systemtap/wiki/SecureBoot?action=AttachFile&do=get&target=Screenshot_kvm-rawhide-64-uefi-1_2014-02-27_14_00_35_crop.png
[confirm]: https://sourceware.org/systemtap/wiki/SecureBoot?action=AttachFile&do=get&target=Screenshot_kvm-rawhide-64-uefi-1_2014-02-27_14_00_44_crop.png
[password]: https://sourceware.org/systemtap/wiki/SecureBoot?action=AttachFile&do=get&target=Screenshot_kvm-rawhide-64-uefi-1_2014-02-27_14_00_53_crop.png
[reboot]: https://sourceware.org/systemtap/wiki/SecureBoot?action=AttachFile&do=get&