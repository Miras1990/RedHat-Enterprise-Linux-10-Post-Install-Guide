# Things to do after installing RedHat Enterprise Linux 10

## Update and Reboot
* Go into the software center and click on update. Alternatively, you can do:
```
sudo dnf refresh
sudo dnf update
```

## Enabling the RPM Fusion repositories
* If you forgot to enable third party repositories during the initial setup window, enable them by pasting the following into the terminal: 
```
sudo dnf install --nogpgcheck https://dl.fedoraproject.org/pub/epel/epel-release-latest-$(rpm -E %rhel).noarch.rpm
sudo dnf install --nogpgcheck https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm
```
sudo subscription-manager repos --enable "codeready-builder-for-rhel-$(rpm -E %{rhel})-$(uname -m)-rpms"
```

## Firmware
* If your system supports firmware update delivery through [lvfs](https://fwupd.org/), update your device firmware by:
```
sudo fwupdmgr refresh --force
sudo fwupdmgr get-devices # Lists devices with available updates.
sudo fwupdmgr get-updates # Fetches list of available updates.
sudo fwupdmgr update
```

## [Intel(R) Graphics Compute Runtime for oneAPI Level Zero and OpenCL(TM) Driver](https://github.com/intel/compute-runtime)
```
sudo dnf install intel-compute-runtime intel-gmmlib intel-igc intel-level-zero intel-ocloc intel-opencl intel-opencl-clang
```

## Media Codecs and H/W Video Acceleration
* Install these to get proper multimedia playback.
````
sudo dnf install ffmpeg
sudo dnf upgrade @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf group install sound-and-video
```

## Microsoft Fonts
```
sudo dnf install curl cabextract xorg-x11-font-utils fontconfig 
sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
```

## DaVinci Resolve (The program works and detects Intel's integrated graphics)
```
* Installation
sudo dnf install apr apr-util mesa-libGLU libxcrypt-compat fuse fuse-libs pango libXt
unzip DaVinci_Resolve_Studio_20.2.1_Linux.zip
chmod +x ./DaVinci_Resolve_Studio_20.2.1_Linux.run
sudo SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_20.2.1_Linux.run -i

* Repair (/opt/resolve/bin/resolve: symbol lookup error: /usr/lib/libpango-1.0.so.0: undefined symbol: g_once_init_leave_pointer):
cd /opt/resolve/libs
sudo mkdir disabled-libraries
sudo mv libglib* disabled-libraries
sudo mv libgio* disabled-libraries
sudo mv libgmodule* disabled-libraries 
```

## Configuring the system and the GNOME graphical environment

### Set Hostname
* `hostnamectl set-hostname YOUR_HOSTNAME`

### Set UTC Time
* Used to counter time inconsistencies in dual boot systems
```
sudo timedatectl set-local-rtc 1
```
### Default Firefox start page 
* The tweak below will make the start page the default firefox start page instead of [this](https://fedoraproject.org/start)
```
sudo rm -f /usr/lib64/firefox/browser/defaults/preferences/all-redhat.js
```

