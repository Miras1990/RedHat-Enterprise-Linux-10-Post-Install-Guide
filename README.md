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
sudo dnf install intel-compute-runtime intel-gmmlib intel-igc intel-level-zero intel-ocloc intel-opencl intel-opencl-clang oneapi-level-zero
```

## Media Codecs and H/W Video Acceleration
* Install these to get proper multimedia playback.
```
sudo dnf install ffmpeg
sudo dnf upgrade @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf group install sound-and-video
```

## Microsoft Fonts
```
sudo dnf install curl cabextract xorg-x11-font-utils fontconfig 
sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
```

## [DaVinci Resolve](https://www.blackmagicdesign.com/pl/products/davinciresolve)
* The program works and detects Intel's integrated graphics!
* Installation
```
sudo dnf install apr apr-util mesa-libGLU libxcrypt-compat fuse fuse-libs pango libXt
unzip DaVinci_Resolve_Studio_20.2.1_Linux.zip
chmod +x ./DaVinci_Resolve_Studio_20.2.1_Linux.run
sudo SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_20.2.1_Linux.run -i
```
* Repair (/opt/resolve/bin/resolve: symbol lookup error: /usr/lib/libpango-1.0.so.0: undefined symbol: g_once_init_leave_pointer):
```
cd /opt/resolve/libs
sudo mkdir disabled-libraries
sudo mv libglib* disabled-libraries
sudo mv libgio* disabled-libraries
sudo mv libgmodule* disabled-libraries 
```

## [DeepCool Digital for Linux](https://github.com/Nortank12/deepcool-digital-linux)

* Automatic Start with Systemd 
```
sudo cp ./deepcool-digital-linux /usr/sbin/
sudo nano /etc/systemd/system/deepcool-digital.service
```
* Insert the following:
```
[Unit]
Description=DeepCool Digital

[Service]
ExecStart=/usr/sbin/deepcool-digital-linux
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```
* Enable the service:
```
sudo systemctl enable deepcool-digital
```
## [LogiOps](https://github.com/PixlOne/logiops)

* Dependencies:
```
sudo dnf install cmake libevdev-devel systemd-devel libconfig-devel gcc-c++ glib2-devel
```
* Building:
```
git clone https://github.com/PixlOne/logiops.git
cd logiops/
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```
* Installation:
```
sudo make install
```
Download [logid.example.cfg](https://github.com/PixlOne/logiops/blob/main/logid.example.cfg) file and copy its contents to "/etc/logid.cfg"
* Enable service
```
sudo systemctl enable --now logid
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
### Enable VAAPI in Firefox "about:config"
```
media.navigator.mediadatadecoder_vpx_enabled  true
layers.acceleration.force-enabled true
gfx.webrender.all true
```

### Enable Support for ntfs-3g and exfat:
```
sudo dnf install exfatprogs ntfs-3g ntfsprogs
```

### Enable Trim Support
```
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
```

### Install lm-sensors and detect all sensors
```
sudo dnf install lm_sensors
sudo modprobe nct6775
sudo systemctl daemon-reload
sudo sensors-detect
```

### Disable `NetworkManager-wait-online.service`
* Disabling it can decrease the boot time by at least ~15s-20s:
```
sudo systemctl disable NetworkManager-wait-online.service
```

### Better Linux Disk Caching & Performance with vm.dirty_ratio & vm.dirty_background_ratio & Sharply reduce swap inclination
```
sudo nano /etc/sysctl.conf
```
```
vm.swappiness=1
vm.vfs_cache_pressure=100
vm.dirty_background_ratio = 5
vm.dirty_background_bytes = 0
vm.dirty_ratio = 10
vm.dirty_bytes = 0
vm.dirty_writeback_centisecs = 500
vm.dirty_expire_centisecs = 3000
```

### Clear RAM memory Cache
```
* To free pagecache:
echo 1 > /proc/sys/vm/drop_caches
* To free dentries and inodes:
echo 2 > /proc/sys/vm/drop_caches
* To free pagecache, dentries and inodes:
echo 3 > /proc/sys/vm/drop_caches
```

### Flatpak
To install Flatpak on Red Hat Enterprise Linux Workstation 8 or older, run the following in a terminal:
```
sudo dnf install flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

### Snap 
```
sudo dnf install snapd
sudo ln -s /var/lib/snapd/snap /snap
sudo reboot now
sudo snap refresh
```

### GNOME volume step adjustment
```
gsettings set org.gnome.settings-daemon.plugins.media-keys volume-step 2
```

### Gnome Extensions and Tweaks
```
sudo dnf install gnome-tweaks
flatpak install flathub com.mattjakeman.ExtensionManager
```

### Deleted Apps
* lvm2
* cockpit
* thermald

## Additional Applications
* 7zip-standalone
* unrar
* Blender
* Discord 
* Spotify
* GIMP
* Handbrake
* Krita
* VLC
* Darktable
* Rawtherapee
* mc
* bpytop
* inxi
* Libreoffice
* Audacity
