---
title: $ linux on macos
date: 2022-07-30
author: Shain Singh
draft: false
---

I bought my first Apple laptop in 2005 as I was keen to leverage native UNIX programs on x86 hardware. Since that time there have a number of packaging managers that I have used ([Fink](https://www.finkproject.org), [Macports](https://www.macports.org)) to now settle on [Homebrew](https://brew.sh) for installing UNIX programs on my laptop.

In recent years I have found that even with a package manager, software libraries and dependencies of non-native versions of Python and Ruby make installation of programs quote bloated. While storage is relatively cheap, I have also started trying to do more with less in terms of laptop hardware resources. 

All of these things have brought me to a point where my primary laptop of choice is currently the Apple M1 Macbook Air (MBA). The form factor, size, weight mean it is perfect for travelling as well as working in the office.

I recently made the choice to rely less on using Homebrew for installing software and start looking at whether I can just leverage a Linux VM instead. This led me to start looking into a Docker container that is a fully fledged Linux operating system. The initial issue with this is that you need a host Linux to run Docker in, which means using virtualisation software. In effect, you are using MacOS to first run a Linux VM using virtualisation technology and then run Docker inside that Linux VM.

The software that makes all this possible on my M1 MBA is [Multipass](https://multipass.run)

**Pros:**
- Virtualisation on MacOS uses the extremely efficient and performant [Hypervisor.Framework](https://developer.apple.com/documentation/hypervisor) 
- No need to install resource intensive software such as Virtualbox or VMWare Fusion

**Cons:**
- Container technology is built to be ephemeral and is not typically a good use case for a full operating system

# Installation

Being able to use Multipass on MacOS is extremely simple and this section is a list of steps that I used.

## Prerequisites

If Homebrew is not currently installed on the laptop, then use the following script:

```Bash

# /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

```

Further instructions are available at [the Homebrew site](https://brew.sh) 

## Multipass

Multipass is installed as a [Cask](https://github.com/Homebrew/homebrew-cask)

```Bash

# brew -v install multipass


==> Downloading https://github.com/canonical/multipass/releases/download/v1.10.1/multipass-1.10.1+mac-Darwin.pkg
Already downloaded: /Users/shsingh/Library/Caches/Homebrew/downloads/521aecca03103c46d7d9cd32bc600c97ed07b4c78b94d67cfa4e08562d920976--multipass-1.10.1+mac-Darwin.pkg
==> Verifying checksum for cask 'multipass'
==> Installing Cask multipass
cp -p /Users/shsingh/Library/Caches/Homebrew/downloads/521aecca03103c46d7d9cd32bc600c97ed07b4c78b94d67cfa4e08562d920976--multipass-1.10.1+mac-Darwin.pkg /opt/homebrew/Caskroom/multipass/1.10.1/multipass-1.10.1+mac-Darwin.pkg
==> Running installer for multipass; your password may be necessary.
Package installers may write to any location; options such as `--appdir` are ignored.
installer: Package name is multipass
installer: Installing at base path /
installer:PHASE:Preparing for installation…
installer:PHASE:Preparing the disk…
installer:PHASE:Preparing multipass…
installer:PHASE:Waiting for other installations to complete…
installer:PHASE:Configuring the installation…
installer:STATUS:
installer:%7.797427
installer:PHASE:Writing files…
installer:%13.491799
installer:PHASE:Writing files…
installer:%20.609764
installer:PHASE:Writing files…
installer:%27.727730
installer:PHASE:Writing files…
installer:%36.269288
installer:PHASE:Writing files…
installer:%41.963660
installer:PHASE:Writing files…
installer:%53.352404
installer:PHASE:Writing files…
installer:%91.903978
installer:PHASE:Registering updated components…
installer:%92.615220
installer:PHASE:Registering updated components…
installer:%93.327476
installer:PHASE:Registering updated components…
installer:%94.038851
installer:PHASE:Registering updated components…
installer:%94.751139
installer:PHASE:Registering updated components…
installer:PHASE:Validating packages…
installer:%97.750000
installer:STATUS:Running installer actions…
installer:STATUS:
installer:PHASE:Finishing the Installation…
installer:STATUS:
installer:%100.000000
installer:PHASE:The software was successfully installed.
installer: The install was successful.
🍺  multipass was successfully installed!


```

## Docker

There is already a prebuilt alias for 'docker' in Multipass, which will launch a new Linux VM.

```Bash

# multipass launch docker
Launched: docker

# multipass list
Name                    State             IPv4             Image
docker                  Running           10.211.56.4      Ubuntu 22.04 LTS
                                          172.17.0.1

```

Once Docker is installed you will need an alias for the 'docker' command.

```Bash

# multipass alias docker:docker

# which -a docker
/Users/shsingh/Library/Application Support/multipass/bin/docker
/Users/shsingh/Library/Application Support/multipass/bin/docker

# docker ps
CONTAINER ID   IMAGE                    COMMAND        CREATED       STATUS       PORTS                                                           NAMES
ba8229e8aa62   portainer/portainer-ce   "/portainer"   2 hours ago   Up 2 hours   8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer

```

## Linux desktop as a container

Once Docker is installed you have all the standard tooling available and you can then run any Linux operating system as a container.

```Bash

# docker run --tty --interactive shsingh/kali-linux-docker-arm64 /bin/zsh
Unable to find image 'shsingh/kali-linux-docker-arm64:latest' locally
latest: Pulling from shsingh/kali-linux-docker-arm64
5282b35103b9: Pull complete
1c931c2ad3fd: Pull complete
6267781a4897: Pull complete
acf0e86eb47b: Pull complete
Digest: sha256:49b8798fa8d91514602669445e36b2173a793b6da55bf8914a717e3b5c750a04
Status: Downloaded newer image for shsingh/kali-linux-docker-arm64:latest
###
Q:      Minnesotans ask, "Why aren't there more pharmacists from Alabama?"
A:      Easy.  It's because they can't figure out how to get the little
        bottles into the typewriter.
###
Linux 986e8b60c680 5.15.0-41-generic #44-Ubuntu SMP Thu Jun 23 11:20:13 UTC 2022 aarch64 GNU/Linux
 10:30:47 up  2:06,  0 users,  load average: 3.25, 1.70, 0.68
###

/
⬢ [Docker] ❯ uname -a
Linux 986e8b60c680 5.15.0-41-generic #44-Ubuntu SMP Thu Jun 23 11:20:13 UTC 2022 aarch64 GNU/Linux

```

# Workflow

As containers are ephemeral, I make sure that any changes I make are regularly committed as a new image and pushed to a container registry. This ensures that the next 'pull' of the image will contain the latest changes. Changes are not common and typically only related to additional software installed or updates done of packages.

To first detach from a running container to do this, you need to use the Ctrl-P followed by Ctrl-Q key combination.

```Bash

# docker ps
CONTAINER ID   IMAGE                    COMMAND        CREATED         STATUS         PORTS                                                           NAMES
986e8b60c680   74000c90faf3             "/bin/zsh"     7 minutes ago   Up 7 minutes                                                                   kali-linux-docker-arm64
ba8229e8aa62   portainer/portainer-ce   "/portainer"   2 hours ago     Up 2 hours     8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer

# docker container commit kali-linux-docker-arm64 shsingh/kali-linux-docker-arm64:latest
sha256:efff31b12c4c2baebf22203a81bdcd5fbed322f2e605a371d08254be7e0324d7

# docker push shsingh/kali-linux-docker-arm64:latest
The push refers to repository [docker.io/shsingh/kali-linux-docker-arm64]
30f8ab718298: Pushed
7d4420eb69bf: Layer already exists
1abaf688267c: Layer already exists
e495438ebf4d: Layer already exists
b45e458bde96: Layer already exists
latest: digest: sha256:98c2d327b74aa8652b6a76485f106068dfd97c02762b7da9341690b3c2eacf86 size: 1381


```

# Additional Thoughts

## Kali Linux as desktop operation system

I've been a user of Kali Linux and its predecessor Backtrack since 2006. Back then I primarily used Backtrack as a Live CD deployment and got used to the tools it included. Over the years Kali Linux has become more of a complete desktop operating system for daily use and is based on Debian. While there is a large list of Linux desktop systems for use, I have found Kali Linux suitable for my needs. If I ever had a need for a native graphical Linux environment then I'd be quite comfortable with Kali Linux for that purpose. A major reason for this is that a large number of pentesting tools are very useful for diagnostics, troubleshooting and passive reconnaisance to get a better understanding of the applications and networks I work with.

Kali Linux also provides [slim Docker images](https://www.kali.org/get-kali/#kali-containers) which can be used as the base. 

```Bash

# docker run --tty --interactive kalilinux/kali-rolling
Unable to find image 'kalilinux/kali-rolling:latest' locally
latest: Pulling from kalilinux/kali-rolling
62c5da721989: Pull complete
Digest: sha256:7012433c7a6843bfc90e1c0ba695fa70b0c0401788a055c08635757fe2f23970
Status: Downloaded newer image for kalilinux/kali-rolling:latest
┌──(root㉿8450debdf88a)-[/]
└─#


```

The base image does not install any tools and there are [metapackages](https://www.kali.org/docs/general-use/metapackages/) which can be used to install the tools needed. This allows me to install the 'kali-linux-headless' metapackage which ensures no extra GUI or X11 packages are unnecessarily installed. While the number of packages installed is quite large, it is worth remembering that is to run a operating system inside the container and this would be a typical build in a standard VM or desktop environment.

```Bash

# apt update; apt-get install kali-linux-headless

Get:1 http://kali.download/kali kali-rolling InRelease [30.6 kB]
Get:2 http://kali.download/kali kali-rolling/contrib arm64 Packages [93.0 kB]
Get:3 http://kali.download/kali kali-rolling/main arm64 Packages [18.2 MB]
Get:4 http://kali.download/kali kali-rolling/non-free arm64 Packages [173 kB]
Fetched 18.5 MB in 2s (8020 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
9 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  adwaita-icon-theme aircrack-ng alsa-topology-conf alsa-ucm-conf amass amass-common apache2 apache2-bin apache2-data apache2-utils apt-file arj arp-scan arping at-spi2-core atftpd attr axel bind9-dnsutils bind9-host bind9-libs binfmt-support binutils binutils-aarch64-linux-gnu
  binutils-common binutils-mingw-w64-i686 binutils-mingw-w64-x86-64 binwalk blt bluez bluez-firmware bluez-hcidump build-essential bulk-extractor bully bundler busybox bzip2 ca-certificates ca-certificates-java cadaver cewl cgpt chntpw cifs-utils clang clang-13 command-not-found
  commix console-setup console-setup-linux cpio cpp cpp-11 crackmapexec cramfsswap creddump7 crunch cryptcat cryptsetup cryptsetup-bin cryptsetup-initramfs cryptsetup-nuke-password curl curlftpfs davtest dbd dbus dbus-bin dbus-daemon dbus-session-bus-common dbus-system-bus-common
  dbus-user-session dconf-gsettings-backend dconf-service dcraw default-jre default-jre-headless default-mysql-server dirb dirmngr distro-info-data dmidecode dmitry dmsetup dns2tcp dnschef dnsenum dnsrecon dos2unix dpkg-dev easy-rsa enum4linux ethtool ettercap-common
  ettercap-graphical evil-winrm exe2hexbat exiv2 expect exploitdb fakeroot ffuf fierce file firebird3.0-common firebird3.0-common-doc firmware-amd-graphics firmware-atheros firmware-intel-sound firmware-iwlwifi firmware-libertas firmware-linux firmware-linux-free
  firmware-linux-nonfree firmware-misc-nonfree firmware-realtek firmware-sof-signed firmware-ti-connectivity firmware-zd1211 flac fontconfig fontconfig-config fonts-dejavu-core fonts-dejavu-extra fonts-font-awesome fonts-freefont-ttf fonts-lato fonts-liberation2 fonts-lyx fping
  freeglut3 freerdp2-x11 freetds-common ftp fuse3 g++ g++-11 galera-4 gawk gcc gcc-11 gcc-11-base gcc-12-base gcc-mingw-w64-base gcc-mingw-w64-i686-win32 gcc-mingw-w64-i686-win32-runtime gcc-mingw-w64-x86-64-win32 gcc-mingw-w64-x86-64-win32-runtime gdal-data gdal-plugins gdisk
  geoip-database git git-man gnupg gnupg-l10n gnupg-utils gpg gpg-agent gpg-wks-client gpg-wks-server gpgconf gpgsm gpp-decrypt graphviz groff-base gsettings-desktop-schemas gss-ntlmssp gtk-update-icon-cache hash-identifier hashcat hashcat-data hashcat-utils hashdeep hashid
  hicolor-icon-theme hping3 hwloc hydra hyperion i2c-tools ibverbs-providers icu-devtools ieee-data ifenslave ifupdown ike-scan impacket-scripts inetsim initramfs-tools initramfs-tools-core iodine iproute2 iptables ipython3 isc-dhcp-client isc-dhcp-common iso-codes isympy-common
  isympy3 iw java-common javascript-common john john-data kali-linux-core kali-linux-firmware kali-tweaks kbd keyboard-configuration keyutils kismet kismet-capture-common kismet-capture-linux-bluetooth kismet-capture-linux-wifi kismet-capture-nrf-51822 kismet-capture-nrf-52840
  kismet-capture-nrf-mousejack kismet-capture-nxp-kw41z kismet-capture-rz-killerbee kismet-capture-ti-cc-2531 kismet-capture-ti-cc-2540 kismet-capture-ubertooth-one kismet-core kismet-logtools klibc-utils kmod krb5-locales laptop-detect laudanum lbd ldap-utils less libaec0
  libafflib0v5 libalgorithm-diff-perl libalgorithm-diff-xs-perl libalgorithm-merge-perl libann0 libaom3 libapache2-mod-php libapache2-mod-php8.1 libapparmor1 libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libapt-pkg-perl libarchive-zip-perl libarchive13 libargon2-1
  libarmadillo11 libarpack2 libasan6 libasound2 libasound2-data libassuan0 libasyncns0 libatk-bridge2.0-0 libatk-wrapper-java libatk-wrapper-java-jni libatk1.0-0 libatk1.0-data libatm1 libatomic1 libatspi2.0-0 libaudio2 libauthen-sasl-perl libavahi-client3 libavahi-common-data
  libavahi-common3 libavcodec59 libavutil57 libbcg729-0 libbfio1 libbinutils libblas3 libblosc1 libbluetooth3 libboost-iostreams1.74.0 libboost-thread1.74.0 libbpf0 libbrotli1 libbsd0 libbson-1.0-0 libbtbb1 libbytes-random-secure-perl libc-ares2 libc-dev-bin libc-devtools libc-l10n
  libc6 libc6-dev libcairo-gobject2 libcairo2 libcap2-bin libcapstone-dev libcapstone4 libcbor0.8 libcc1-0 libccid libcdt5 libcephfs2 libcfitsio9 libcgi-fast-perl libcgi-pm-perl libcgraph6 libclang-common-13-dev libclang-cpp13 libclang1-13 libcli1.10 libclone-perl libcodec2-1.0
  libcolord2 libcommon-sense-perl libconfig-inifiles-perl libconfig9 libcrypt-dev libcrypt-random-seed-perl libcrypt-ssleay-perl libcrypto++8 libcryptsetup12 libct4 libctf-nobfd0 libctf0 libcups2 libcurl3-gnutls libcurl4 libdata-dump-perl libdate-manip-perl libdatrie1 libdav1d6
  libdaxctl1 libdbd-mariadb-perl libdbi-perl libdbus-1-3 libdconf1 libde265-0 libdecor-0-0 libdecor-0-plugin-1-cairo libdeflate0 libdevmapper1.02.1 libdigest-bubblebabble-perl libdigest-hmac-perl libdouble-conversion3 libdpkg-perl libdrm-amdgpu1 libdrm-common libdrm-nouveau2
  libdrm-radeon1 libdrm2 libdw1 libedit2 libegl-mesa0 libegl1 libelf1 libencode-locale-perl libepoxy0 liberror-perl libev4 libevdev2 libevent-2.1-7 libevent-core-2.1-7 libevent-openssl-2.1-7 libevent-pthreads-2.1-7 libewf2 libexiv2-27 libexpat1 libexpat1-dev libexporter-tiny-perl
  libfakeroot libfbclient2 libfcgi-bin libfcgi-perl libfcgi0ldbl libfdisk1 libffi-dev libfido2-1 libfile-fcntllock-perl libfile-listing-perl libflac8 libflashrom1 libfluidsynth3 libfmt8 libfont-afm-perl libfontconfig1 libfontenc1 libfreerdp-client2-2 libfreerdp2-2 libfreetype6
  libfreexl1 libfribidi0 libfstrm0 libftdi1-2 libfuse2 libfuse3-3 libfyba0 libgbm1 libgc1 libgcc-11-dev libgcc-s1 libgd3 libgdal31 libgdbm-compat4 libgdbm6 libgdk-pixbuf-2.0-0 libgdk-pixbuf2.0-bin libgdk-pixbuf2.0-common libgeoip1 libgeos-c1v5 libgeos3.11.0 libgeotiff5 libgfapi0
  libgfortran5 libgfrpc0 libgfxdr0 libgif7 libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-0 libglib2.0-data libglu1-mesa libglusterfs0 libglvnd0 libglx-mesa0 libglx0 libgmp-dev libgmpxx4ldbl libgomp1 libgpgme11 libgpm2 libgprofng0 libgraphite2-3 libgsm1 libgssapi-krb5-2 libgtk-3-0
  libgtk-3-bin libgtk-3-common libgts-0.7-5 libgts-bin libgudev-1.0-0 libgvc6 libgvpr2 libharfbuzz0b libhdf4-0-alt libhdf5-103-1 libhdf5-hl-100 libheif1 libhtml-form-perl libhtml-format-perl libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl libhtml-tree-perl
  libhttp-cookies-perl libhttp-daemon-perl libhttp-date-perl libhttp-dav-perl libhttp-message-perl libhttp-negotiate-perl libhttp-server-simple-perl libhwasan0 libhwloc-plugins libhwloc15 libi2c0 libibverbs1 libice6 libicu-dev libicu71 libidn12 libimage-exiftool-perl libimagequant0
  libiniparser1 libinput-bin libinput10 libinstpatch-1.0-2 libio-html-perl libio-multiplex-perl libio-socket-inet6-perl libio-socket-ssl-perl libip4tc2 libip6tc2 libipc-shareable-perl libisl23 libitm1 libiw30 libjack-jackd2-0 libjansson4 libjbig0 libjemalloc2 libjpeg-turbo-progs
  libjpeg62-turbo libjs-jquery libjs-jquery-ui libjs-skeleton libjs-sphinxdoc libjs-underscore libjson-c5 libjson-perl libjson-xs-perl libjudydebian1 libk5crypto3 libkeyutils1 libklibc libkmlbase1 libkmldom1 libkmlengine1 libkmod2 libkrb5-3 libkrb5support0 libksba8 liblab-gamut1
  liblapack3 liblbfgsb0 liblcms2-2 libldap-2.5-0 libldap-common libldb2 liblerc3 liblinear4 liblist-moreutils-perl liblist-moreutils-xs-perl libllvm13 libllvm14 liblmdb0 liblocale-gettext-perl liblsan0 libltdl7 liblua5.2-0 liblua5.3-0 libluajit-5.1-2 libluajit-5.1-common
  liblwp-mediatypes-perl liblwp-protocol-https-perl liblz4-dev liblzo2-2 libmagic-dev libmagic-mgc libmagic1 libmailtools-perl libmariadb3 libmath-random-isaac-perl libmath-random-isaac-xs-perl libmaxminddb0 libmd0 libmd4c0 libmemcached11 libmime-charset-perl libminizip1 libmnl0
  libmodplug1 libmongoc-1.0-0 libmongocrypt0 libmotif-common libmp3lame0 libmpc3 libmpdec3 libmpfr6 libmpg123-0 libmtdev1 libncurses-dev libncurses5 libncurses6 libncursesw6 libndctl6 libneon27-gnutls libnet-cidr-perl libnet-dns-perl libnet-dns-sec-perl libnet-http-perl
  libnet-ip-perl libnet-libidn-perl libnet-netmask-perl libnet-server-perl libnet-smtp-ssl-perl libnet-snmp-perl libnet-ssleay-perl libnet-whois-ip-perl libnet1 libnetcdf19 libnetfilter-conntrack3 libnetfilter-queue1 libnewt0.52 libnfnetlink0 libnfsidmap1 libnftables1 libnftnl11
  libnghttp2-14 libnginx-mod-http-geoip libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream libnginx-mod-stream-geoip libnl-3-200 libnl-genl-3-200 libnl-route-3-200 libnm0 libnpth0 libnsl-dev libnsl2 libnspr4 libnss-systemd libnss3
  libntfs-3g89 libnuma1 libnumber-bytes-human-perl libobjc-11-dev libobjc4 libodbc2 libodbcinst2 libogdi4.1 libogg0 libopenal-data libopenal1 libopengl0 libopenjp2-7 libopus0 libopusfile0 libout123-0 libpam-cap libpam-systemd libpango-1.0-0 libpangocairo-1.0-0 libpangoft2-1.0-0
  libparted2 libpathplan4 libpcap0.8 libpci3 libpciaccess0 libpcre2-16-0 libpcsclite1 libperl4-corelibs-perl libperl5.34 libpfm4 libpipeline1 libpixman-1-0 libpkcs11-helper1 libpmem1 libpng16-16 libpocl2 libpocl2-common libpod-parser-perl libpolkit-agent-1-0 libpolkit-gobject-1-0
  libpoppler118 libpopt0 libportaudio2 libportmidi0 libpq5 libprocps8 libproj25 libprotobuf-c1 libprotobuf23 libproxychains4 libpsl5 libpulse0 libpython2-stdlib libpython2.7-minimal libpython2.7-stdlib libpython3-dev libpython3-stdlib libpython3.10 libpython3.10-dev
  libpython3.10-minimal libpython3.10-stdlib libqhull-r8.0 libqt5core5a libqt5dbus5 libqt5designer5 libqt5gui5 libqt5help5 libqt5network5 libqt5opengl5 libqt5printsupport5 libqt5sql5 libqt5sql5-sqlite libqt5svg5 libqt5test5 libqt5widgets5 libqt5xml5 libradare2-5.0.0 libradare2-common
  libradare2-dev librados2 libraqm0 librdmacm1 libreadline8 libregexp-assemble-perl libregexp-ipv6-perl librsvg2-2 librsvg2-common librtlsdr0 librtmp1 librttopo1 libruby3.0 libsamplerate0 libsasl2-2 libsasl2-modules libsasl2-modules-db libsbc1 libsdl2-2.0-0 libsdl2-image-2.0-0
  libsdl2-mixer-2.0-0 libsdl2-ttf-2.0-0 libsensors-config libsensors5 libserf-1-1 libshine3 libsigsegv2 libslang2 libsm6 libsmbclient libsmi2ldbl libsnappy1v5 libsndfile1 libsndio7.0 libsnmp-base libsnmp40 libsocket6-perl libsodium23 libsombok3 libsoxr0 libspandsp2 libspatialite7
  libspeex1 libspeexdsp1 libsqlite3-0 libssh-4 libssh-gcrypt-4 libssh2-1 libssl1.1 libssl3 libstdc++-11-dev libstdc++6 libstring-crc32-perl libstring-random-perl libsuperlu5 libsvn1 libsvtav1enc0 libswresample4 libswscale6 libsybdb5 libsyn123-0 libsystemd-shared libsz2 libtalloc2
  libtcl8.6 libtdb1 libterm-readkey-perl libtevent0 libthai-data libthai0 libtheora0 libtiff5 libtimedate-perl libtinfo-dev libtinfo5 libtirpc-common libtirpc-dev libtirpc3 libtk8.6 libtommath1 libtry-tiny-perl libtsan0 libtsk19 libturbojpeg0 libtwolame0 libtypes-serialiser-perl
  libubertooth1 libubsan1 libuchardet0 libucl1 libunicode-linebreak-perl libunsafessl1.0.2 liburcu8 liburi-perl liburing2 liburiparser1 libusb-1.0-0 libutempter0 libutf8proc2 libuv1 libuv1-dev libva-drm2 libva-x11-2 libva2 libvdpau-va-gl1 libvdpau1 libvhdi1 libvmdk1 libvorbis0a
  libvorbisenc2 libvorbisfile3 libvpx7 libvulkan1 libwacom-bin libwacom-common libwacom9 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libwbclient0 libwebp7 libwebpdemux2 libwebpmux3 libwebsockets16 libwinpr2-2 libwireshark-data libwireshark15 libwiretap12
  libwrap0 libwsutil13 libwww-mechanize-perl libwww-perl libwww-robotrules-perl libx11-6 libx11-data libx11-xcb1 libx264-164 libx265-199 libxau6 libxaw7 libxcb-dri2-0 libxcb-dri3-0 libxcb-glx0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-present0 libxcb-randr0
  libxcb-render-util0 libxcb-render0 libxcb-shape0 libxcb-shm0 libxcb-sync1 libxcb-util1 libxcb-xfixes0 libxcb-xinerama0 libxcb-xinput0 libxcb-xkb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxerces-c3.2 libxext6 libxfixes3 libxft2 libxi6 libxinerama1
  libxkbcommon-x11-0 libxkbcommon0 libxkbfile1 libxm4 libxml-dom-perl libxml-parser-perl libxml-perl libxml-regexp-perl libxml-writer-perl libxml2 libxml2-dev libxml2-utils libxmu6 libxmuu1 libxnvctrl0 libxpm4 libxrandr2 libxrender1 libxshmfence1 libxslt1.1 libxss1 libxt6
  libxtables12 libxtst6 libxv1 libxvidcore4 libxxf86dga1 libxxf86vm1 libyaml-0-2 libyara9 libz3-4 libz3-dev libzip-dev libzip4 libzvbi-common libzvbi0 linux-base linux-libc-dev llvm-13 llvm-13-dev llvm-13-linker-tools llvm-13-runtime llvm-13-tools locales logrotate lrzsz lsb-release
  lsof lua-lpeg macchanger magicrescue mailcap make manpages manpages-dev mariadb-client-10.6 mariadb-client-core-10.6 mariadb-common mariadb-server-10.6 mariadb-server-core-10.6 maskprocessor masscan media-types mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers
  metasploit-framework mime-support mimikatz mingw-w64-common mingw-w64-i686-dev mingw-w64-x86-64-dev minicom miredo mitmproxy mpg123 msfpc mtd-utils multimac mysql-common nasm nbtscan ncompress ncrack ncurses-hexedit ncurses-term net-tools netbase netcat-traditional netdiscover
  netmask netsed netsniff-ng nfs-common nftables nginx nginx-common nginx-core ngrep nikto nmap nmap-common node-normalize.css ntfs-3g ntpsec ocl-icd-libopencl1 offsec-awae-python2 onesixtyone openjdk-11-jre openjdk-11-jre-headless opensc opensc-pkcs11 openssh-client openssh-server
  openssh-sftp-server openssl openvpn p7zip p7zip-full parted passing-the-hash patator patch pci.ids pcscd pdf-parser pdfid perl perl-base perl-modules-5.34 perl-openssl-defaults php php-common php-mysql php8.1 php8.1-cli php8.1-common php8.1-mysql php8.1-opcache php8.1-readline
  pinentry-curses pipal pixiewps pkexec plocate pocl-opencl-icd polenum policykit-1 polkitd poppler-data postgresql postgresql-14 postgresql-client-14 postgresql-client-common postgresql-common powershell-empire powersploit procps proj-bin proj-data proxychains4 proxytunnel psmisc
  ptunnel publicsuffix pwnat pyqt5-dev-tools python-apt-common python-babel-localedata python-cffi python-cffi-backend python-is-python3 python-matplotlib-data python2 python2-minimal python2.7 python2.7-minimal python3 python3-adblockparser python3-aiocmd python3-aioconsole
  python3-aiodns python3-aiofiles python3-aiohttp python3-aiomultiprocess python3-aiosignal python3-aiosmb python3-aiosqlite python3-aiowinreg python3-ajpy python3-altgraph python3-aniso8601 python3-anyio python3-appdirs python3-apt python3-asciitree python3-asgiref
  python3-asn1crypto python3-async-timeout python3-asysocks python3-attr python3-automat python3-babel python3-backcall python3-backoff python3-bcrypt python3-bidict python3-binwalk python3-blinker python3-bluepy python3-brotli python3-bs4 python3-censys python3-certifi
  python3-cffi-backend python3-chardet python3-charset-normalizer python3-cheroot python3-cherrypy-cors python3-cherrypy3 python3-click python3-click-plugins python3-colorama python3-commonmark python3-constantly python3-cryptography python3-cycler python3-dataclasses-json
  python3-dateutil python3-decorator python3-dev python3-dicttoxml python3-distlib python3-distutils python3-dnslib python3-dnspython python3-docopt python3-docx python3-donut python3-dotenv python3-dropbox python3-dsinternals python3-engineio python3-et-xmlfile python3-exifread
  python3-fastapi python3-filelock python3-flasgger python3-flask python3-flask-restful python3-flask-socketio python3-fonttools python3-frozenlist python3-fs python3-future python3-gdal python3-gexf python3-gpg python3-greenlet python3-h11 python3-h2 python3-hamcrest python3-hpack
  python3-html5lib python3-httpagentparser python3-humanize python3-hyperframe python3-hyperlink python3-idna python3-impacket python3-importlib-metadata python3-incremental python3-iniconfig python3-invoke python3-ipwhois python3-ipy python3-ipython python3-itsdangerous
  python3-jaraco.classes python3-jaraco.collections python3-jaraco.context python3-jaraco.functools python3-jaraco.text python3-jdcal python3-jedi python3-jinja2 python3-jq python3-jsonschema python3-kaitaistruct python3-kismetcapturebtgeiger python3-kismetcapturefreaklabszigbee
  python3-kismetcapturertl433 python3-kismetcapturertladsb python3-kismetcapturertlamr python3-kiwisolver python3-ldap3 python3-ldapdomaindump python3-ldb python3-lib2to3 python3-limiter python3-limits python3-lsassy python3-lxml python3-lz4 python3-macholib python3-magic
  python3-mako python3-markdown python3-markupsafe python3-marshmallow python3-marshmallow-enum python3-matplotlib python3-matplotlib-inline python3-mechanize python3-minidump python3-minikerberos python3-minimal python3-mistune0 python3-more-itertools python3-mpmath python3-msgpack
  python3-msldap python3-multidict python3-mypy-extensions python3-mysqldb python3-nacl python3-neo4j python3-neobolt python3-neotime python3-netaddr python3-netifaces python3-networkx python3-newt python3-ntlm-auth python3-ntp python3-numpy python3-olefile python3-opengl
  python3-openpyxl python3-openssl python3-packaging python3-paramiko python3-parso python3-passlib python3-pcapy python3-pefile python3-pexpect python3-phonenumbers python3-pickleshare python3-pil python3-pil.imagetk python3-pip python3-pip-whl python3-pkg-resources
  python3-platformdirs python3-pluggy python3-pluginbase python3-ply python3-portend python3-pptx python3-prettytable python3-prompt-toolkit python3-protobuf python3-psycopg2 python3-ptyprocess python3-publicsuffix2 python3-publicsuffixlist python3-py python3-pyasn1
  python3-pyasn1-modules python3-pycares python3-pycryptodome python3-pycurl python3-pydantic python3-pydispatch python3-pydot python3-pyee python3-pygame python3-pygments python3-pygraphviz python3-pyinotify python3-pyinstaller python3-pylnk3 python3-pyminifier python3-pymssql
  python3-pymysql python3-pyparsing python3-pypdf2 python3-pyperclip python3-pyppeteer python3-pypsrp python3-pypykatz python3-pyqt5 python3-pyqt5.qtopengl python3-pyqt5.sip python3-pyqtgraph python3-pyrsistent python3-pysmi python3-pysnmp4 python3-pytest python3-pyvnc
  python3-pywerview python3-qrcode python3-redis python3-repoze.lru python3-requests python3-requests-ntlm python3-requests-toolbelt python3-responses python3-retrying python3-rich python3-routes python3-rq python3-ruamel.yaml python3-ruamel.yaml.clib python3-samba python3-scapy
  python3-scipy python3-secure python3-serial python3-service-identity python3-setuptools python3-setuptools-whl python3-shodan python3-simplejson python3-six python3-slowapi python3-sniffio python3-socketio python3-socks python3-sortedcontainers python3-soupsieve python3-spnego
  python3-spyse python3-sqlalchemy python3-sqlalchemy-ext python3-sqlalchemy-utc python3-starlette python3-stone python3-sympy python3-talloc python3-tdb python3-tempora python3-termcolor python3-terminaltables python3-texttable python3-tk python3-token-bucket python3-tomli
  python3-tornado python3-tqdm python3-traitlets python3-twisted python3-typing-extensions python3-typing-inspect python3-tz python3-ufolib2 python3-ujson python3-unicodecsv python3-unicodedata2 python3-urllib3 python3-urwid python3-uvicorn python3-uvloop python3-virtualenv
  python3-wcwidth python3-webencodings python3-webob python3-websocket python3-websockets python3-websockify python3-werkzeug python3-wheel python3-wheel-whl python3-whois python3-winacl python3-wsproto python3-xlrd python3-xlsxwriter python3-xlutils python3-xlwt python3-xmltodict
  python3-yaml python3-yara python3-yarl python3-zc.lockfile python3-zipp python3-zlib-wrapper python3-zope.interface python3.10 python3.10-dev python3.10-minimal qsslcaudit qt5-gtk-platformtheme qtbase5-dev-tools qtchooser qttranslations5-l10n racc radare2 rake read-edid
  readline-common reaver rebind recon-ng redsocks responder rfkill rpcbind rpcsvc-proto rsmangler rsync ruby ruby-activesupport ruby-addressable ruby-builder ruby-bundler ruby-cms-scanner ruby-concurrent ruby-dev ruby-domain-name ruby-erubi ruby-ethon ruby-ffi ruby-get-process-mem
  ruby-gssapi ruby-gyoku ruby-http-cookie ruby-httpclient ruby-i18n ruby-ipaddress ruby-little-plugger ruby-logging ruby-mime ruby-mime-types ruby-mime-types-data ruby-mini-exiftool ruby-mini-portile2 ruby-multi-json ruby-net-http-digest-auth ruby-net-telnet ruby-nokogiri ruby-nori
  ruby-ntlm ruby-oj ruby-opt-parse-validator ruby-pkg-config ruby-progressbar ruby-public-suffix ruby-rchardet ruby-rubygems ruby-snmp ruby-spider ruby-sqlite3 ruby-typhoeus ruby-tzinfo ruby-unf ruby-unf-ext ruby-webrick ruby-winrm ruby-winrm-fs ruby-xmlrpc ruby-yajl ruby-zeitwerk
  ruby-zip ruby3.0 ruby3.0-dev ruby3.0-doc rubygems-integration runit-helper sakis3g samba samba-common samba-common-bin samba-dsdb-modules samba-libs samba-vfs-modules samdump2 sbd scalpel screen scrounge-ntfs sendemail sensible-utils set shared-mime-info skipfish sleuthkit
  smbclient smbmap snmp snmpcheck snmpd socat sphinx-rtd-theme-common spiderfoot spike spooftooph sqlite3 sqlmap sqsh squashfs-tools ssl-cert ssldump sslh sslscan sslsplit statsprocessor stunnel4 sudo swaks sysstat systemd systemd-sysv tasksel tasksel-data tcl-expect tcl8.6 tcpdump
  tcpick tcpreplay tdb-tools telnet testdisk tftp thc-ipv6 thc-pptp-bruter theharvester timgm6mb-soundfont tk8.6-blt2.5 tmux tnftp traceroute tree tshark ucf udev udptunnel unicode-data unicorn-magic unix-privesc-check unixodbc-common unrar unzip update-inetd upx-ucl usbutils
  va-driver-all vboot-kernel-utils vboot-utils vdpau-driver-all vim vim-common vim-runtime vim-tiny vlan voiphopper vpnc vpnc-scripts wafw00f wce webshells weevely wfuzz wget whatweb whois wifite windows-binaries winexe wireless-regdb wireless-tools wireshark-common wordlists wpscan
  x11-common x11-utils xauth xdg-user-dirs xkb-data xxd xz-utils zip zlib1g-dev zsh zsh-autosuggestions zsh-common zsh-syntax-highlighting zstd
Suggested packages:
  gpsd apache2-doc apache2-suexec-pristine | apache2-suexec-custom www-browser binutils-doc blt-demo pulseaudio-module-bluetooth bzip2-doc winbind bash-completion clang-13-doc snapd libarchive1 cpp-doc gcc-11-locales dosfstools gphoto2 netpbm pinentry-gnome3 tor debian-keyring tk8.6
  gcc-11-doc gawk-doc gcc-multilib autoconf automake libtool flex bison gdb gcc-doc gcc-10-locales gettext-base git-daemon-run | git-daemon-sysvinit git-doc git-email git-gui gitk gitweb git-cvs git-mediawiki git-svn parcimonie xloadimage scdaemon gsfonts graphviz-doc groff
  beignet-opencl-icd nvidia-opencl-icd mesa-opencl-icd hydra-gtk libi2c-dev python3-smbus ppp rdnssd ipcalc network-manager-iodine network-manager-iodine-gnome iproute2-doc firewalld resolvconf avahi-autoipd isc-dhcp-client-ddns isoquery wordlist kismet-doc kismet-plugins festival
  libsasl2-modules-gssapi-mit | libsasl2-modules-gssapi-heimdal php-pear lrzip libasound2-plugins alsa-utils nas libgssapi-perl libcuda1 libnvcuvid1 libnvidia-encode1 glibc-doc libnss-nis libnss-nisplus pcmciautils colord cups-common libmldbm-perl libnet-daemon-perl
  libsql-statement-perl bzr libgd-tools gdbm-l10n geoip-bin geotiff-bin gdal-bin libgeotiff-epsg gmp-doc libgmp10-doc libmpfr-dev gpm krb5-doc krb5-user gvfs libhdf4-doc libhdf4-alt-dev hdf4-tools libipc-sharedcache-perl icu-doc libposix-strptime-perl jackd2 libjs-jquery-ui-docs
  liblcms2-utils liblinear-tools liblinear-dev mmdb-bin libencode-hanextra-perl libpod2-base-perl ncurses-doc liblog-log4perl-perl libcrypt-des-perl odbc-postgresql tdsodbc ogdi-bin opus-tools libparted-dev libparted-i18n pciutils pulseaudio qt5-image-formats-plugins qtwayland5
  librsvg2-bin libsasl2-modules-ldap libsasl2-modules-otp libsasl2-modules-sql xdg-utils lm-sensors snmp-mibs-downloader sndiod speex libstdc++-11-doc libsub-name-perl libbusiness-isbn-perl geoipupdate geoip-database-extra libjs-leaflet libjs-leaflet.markercluster wireshark-doc
  libauthen-ntlm-perl pkg-config llvm-13-doc bsd-mailx | mailx make-doc man-browser mailx mariadb-test netcat-openbsd clamav clamav-daemon wine wine64 jackd oss-compat oss4-base open-iscsi watchdog fcgiwrap nginx-doc ncat ndiff zenmap libjs-html5shiv apparmor certbot ntpsec-doc
  ntpsec-ntpviz libnss-mdns fonts-ipafont-gothic fonts-ipafont-mincho fonts-wqy-microhei | fonts-wqy-zenhei fonts-indic keychain libpam-ssh monkeysphere ssh-askpass molly-guard ufw openvpn-systemd-resolved p7zip-rar parted-doc ed diffutils-doc perl-doc libterm-readline-gnu-perl
  | libterm-readline-perl-perl libtap-harness-archive-perl debhelper pinentry-doc poppler-utils ghostscript fonts-japanese-mincho | fonts-ipafont-mincho fonts-japanese-gothic | fonts-ipafont-gothic fonts-arphic-ukai fonts-arphic-uming fonts-nanum postgresql-doc postgresql-doc-14 ssh
  python2-doc python-tk python2.7-doc python3-doc python3-venv python-aioconsole-doc python-aiosqlite-doc python-altgraph-doc python3-apt-dbg python-apt-doc python-attr-doc python-blinker-doc python-bluepy-doc python-censys-doc python-cryptography-doc python3-cryptography-vectors
  python-cycler-doc python3-trio python-donut-doc python-flask-doc python-future-doc python-gexf-doc python-greenlet-dev python-greenlet-doc python3-genshi python-invoke-doc python-ipwhois-doc python-ipython-doc python-jinja2-doc python-jsonschema-doc python3-pymemcache
  python3-rediscluster python-limits-doc python-lxml-doc python-macholib-doc python3-beaker python-mako-doc python-markdown-doc cm-super-minimal dvipng ffmpeg fonts-staypuft gir1.2-gtk-3.0 inkscape python-matplotlib-doc python3-cairocffi python3-gi python3-gi-cairo python3-gobject
  python3-sip texlive-extra-utils texlive-latex-extra python-mpmath-doc python3-gmpy2 python-nacl-doc python-neo4j-doc python-netaddr-docs python-networkx-doc gfortran python-numpy-doc libgle3 python-openssl-doc python3-openssl-dbg python3-gssapi python-pexpect-doc python-pil-doc
  python-ply-doc python-pptx-doc python-psycopg2-doc subversion python-pycares-doc libcurl4-gnutls-dev python-pycurl-doc python-pydispatch-doc python-pygame-doc timidity python-pygments-doc ttf-bitstream-vera python-pygraphviz-doc python-pyinotify-doc python-pyminifier-doc
  python-pymysql-doc python-pyparsing-doc python-pyppeteer-doc python-pyqtgraph-doc python3-hiredis python-requests-doc python3-paste python3-pyx sox wireshark python-scipy-doc python3-wxgtk3.0 | python3-wxgtk python-setuptools-doc python-shodan-doc python-sortedcontainers-doc
  python-pyspnego-doc python-sqlalchemy-doc python3-fdb python3-asyncpg python3-databases python3-multipart texlive-fonts-extra python-sympy-doc python3-terminaltables-doc tix python3-tk-dbg python-tornado-doc python3-pampy python3-wxgtk4.0 python-urwid-doc python-uvicorn-doc
  python2-pip-whl python2-setuptools-whl python-webob-doc python-werkzeug-doc python3-watchdog python-xlutils-doc python-xlrt-doc python3.10-venv python3.10-doc readline-doc python3-braceexpand ri bind9 bind9utils ctdb ldb-tools ntp | chrony smbldap-tools heimdal-clients ophcrack
  byobu | screenie | iselect sendmail-bin autopsy mac-robber snmptrapd sqlite3-doc openbsd-inetd | inet-superserver logcheck-database isag systemd-container systemd-homed systemd-userdbd systemd-boot libtss2-esys-3.0.2-0 libtss2-mu0 libtss2-rc0 tcl-tclreadline fluid-soundfont-gm
  nvidia-vdpau-driver ctags vim-doc vim-scripts indent dnsmasq hcxdumptool hcxpcaptool mesa-utils zsh-doc
Recommended packages:
  intel-microcode amd64-microcode libnfqueue-perl xar bomutils powershell dotnet-sdk-3.1 python3-rekall-core volatility3
The following NEW packages will be installed:
  adwaita-icon-theme aircrack-ng alsa-topology-conf alsa-ucm-conf amass amass-common apache2 apache2-bin apache2-data apache2-utils apt-file arj arp-scan arping at-spi2-core atftpd attr axel bind9-dnsutils bind9-host bind9-libs binfmt-support binutils binutils-aarch64-linux-gnu
  binutils-common binutils-mingw-w64-i686 binutils-mingw-w64-x86-64 binwalk blt bluez bluez-firmware bluez-hcidump build-essential bulk-extractor bully bundler busybox bzip2 ca-certificates ca-certificates-java cadaver cewl cgpt chntpw cifs-utils clang clang-13 command-not-found
  commix console-setup console-setup-linux cpio cpp cpp-11 crackmapexec cramfsswap creddump7 crunch cryptcat cryptsetup cryptsetup-bin cryptsetup-initramfs cryptsetup-nuke-password curl curlftpfs davtest dbd dbus dbus-bin dbus-daemon dbus-session-bus-common dbus-system-bus-common
  dbus-user-session dconf-gsettings-backend dconf-service dcraw default-jre default-jre-headless default-mysql-server dirb dirmngr distro-info-data dmidecode dmitry dmsetup dns2tcp dnschef dnsenum dnsrecon dos2unix dpkg-dev easy-rsa enum4linux ethtool ettercap-common
  ettercap-graphical evil-winrm exe2hexbat exiv2 expect exploitdb fakeroot ffuf fierce file firebird3.0-common firebird3.0-common-doc firmware-amd-graphics firmware-atheros firmware-intel-sound firmware-iwlwifi firmware-libertas firmware-linux firmware-linux-free
  firmware-linux-nonfree firmware-misc-nonfree firmware-realtek firmware-sof-signed firmware-ti-connectivity firmware-zd1211 flac fontconfig fontconfig-config fonts-dejavu-core fonts-dejavu-extra fonts-font-awesome fonts-freefont-ttf fonts-lato fonts-liberation2 fonts-lyx fping
  freeglut3 freerdp2-x11 freetds-common ftp fuse3 g++ g++-11 galera-4 gawk gcc gcc-11 gcc-11-base gcc-mingw-w64-base gcc-mingw-w64-i686-win32 gcc-mingw-w64-i686-win32-runtime gcc-mingw-w64-x86-64-win32 gcc-mingw-w64-x86-64-win32-runtime gdal-data gdal-plugins gdisk geoip-database git
  git-man gnupg gnupg-l10n gnupg-utils gpg gpg-agent gpg-wks-client gpg-wks-server gpgconf gpgsm gpp-decrypt graphviz groff-base gsettings-desktop-schemas gss-ntlmssp gtk-update-icon-cache hash-identifier hashcat hashcat-data hashcat-utils hashdeep hashid hicolor-icon-theme hping3
  hwloc hydra hyperion i2c-tools ibverbs-providers icu-devtools ieee-data ifenslave ifupdown ike-scan impacket-scripts inetsim initramfs-tools initramfs-tools-core iodine iproute2 iptables ipython3 isc-dhcp-client isc-dhcp-common iso-codes isympy-common isympy3 iw java-common
  javascript-common john john-data kali-linux-core kali-linux-firmware kali-linux-headless kali-tweaks kbd keyboard-configuration keyutils kismet kismet-capture-common kismet-capture-linux-bluetooth kismet-capture-linux-wifi kismet-capture-nrf-51822 kismet-capture-nrf-52840
  kismet-capture-nrf-mousejack kismet-capture-nxp-kw41z kismet-capture-rz-killerbee kismet-capture-ti-cc-2531 kismet-capture-ti-cc-2540 kismet-capture-ubertooth-one kismet-core kismet-logtools klibc-utils kmod krb5-locales laptop-detect laudanum lbd ldap-utils less libaec0
  libafflib0v5 libalgorithm-diff-perl libalgorithm-diff-xs-perl libalgorithm-merge-perl libann0 libaom3 libapache2-mod-php libapache2-mod-php8.1 libapparmor1 libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libapt-pkg-perl libarchive-zip-perl libarchive13 libargon2-1
  libarmadillo11 libarpack2 libasan6 libasound2 libasound2-data libassuan0 libasyncns0 libatk-bridge2.0-0 libatk-wrapper-java libatk-wrapper-java-jni libatk1.0-0 libatk1.0-data libatm1 libatomic1 libatspi2.0-0 libaudio2 libauthen-sasl-perl libavahi-client3 libavahi-common-data
  libavahi-common3 libavcodec59 libavutil57 libbcg729-0 libbfio1 libbinutils libblas3 libblosc1 libbluetooth3 libboost-iostreams1.74.0 libboost-thread1.74.0 libbpf0 libbrotli1 libbsd0 libbson-1.0-0 libbtbb1 libbytes-random-secure-perl libc-ares2 libc-dev-bin libc-devtools libc-l10n
  libc6-dev libcairo-gobject2 libcairo2 libcap2-bin libcapstone-dev libcapstone4 libcbor0.8 libcc1-0 libccid libcdt5 libcephfs2 libcfitsio9 libcgi-fast-perl libcgi-pm-perl libcgraph6 libclang-common-13-dev libclang-cpp13 libclang1-13 libcli1.10 libclone-perl libcodec2-1.0 libcolord2
  libcommon-sense-perl libconfig-inifiles-perl libconfig9 libcrypt-dev libcrypt-random-seed-perl libcrypt-ssleay-perl libcrypto++8 libcryptsetup12 libct4 libctf-nobfd0 libctf0 libcups2 libcurl3-gnutls libcurl4 libdata-dump-perl libdate-manip-perl libdatrie1 libdav1d6 libdaxctl1
  libdbd-mariadb-perl libdbi-perl libdbus-1-3 libdconf1 libde265-0 libdecor-0-0 libdecor-0-plugin-1-cairo libdeflate0 libdevmapper1.02.1 libdigest-bubblebabble-perl libdigest-hmac-perl libdouble-conversion3 libdpkg-perl libdrm-amdgpu1 libdrm-common libdrm-nouveau2 libdrm-radeon1
  libdrm2 libdw1 libedit2 libegl-mesa0 libegl1 libelf1 libencode-locale-perl libepoxy0 liberror-perl libev4 libevdev2 libevent-2.1-7 libevent-core-2.1-7 libevent-openssl-2.1-7 libevent-pthreads-2.1-7 libewf2 libexiv2-27 libexpat1 libexpat1-dev libexporter-tiny-perl libfakeroot
  libfbclient2 libfcgi-bin libfcgi-perl libfcgi0ldbl libfdisk1 libffi-dev libfido2-1 libfile-fcntllock-perl libfile-listing-perl libflac8 libflashrom1 libfluidsynth3 libfmt8 libfont-afm-perl libfontconfig1 libfontenc1 libfreerdp-client2-2 libfreerdp2-2 libfreetype6 libfreexl1
  libfribidi0 libfstrm0 libftdi1-2 libfuse2 libfuse3-3 libfyba0 libgbm1 libgc1 libgcc-11-dev libgd3 libgdal31 libgdbm-compat4 libgdbm6 libgdk-pixbuf-2.0-0 libgdk-pixbuf2.0-bin libgdk-pixbuf2.0-common libgeoip1 libgeos-c1v5 libgeos3.11.0 libgeotiff5 libgfapi0 libgfortran5 libgfrpc0
  libgfxdr0 libgif7 libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-0 libglib2.0-data libglu1-mesa libglusterfs0 libglvnd0 libglx-mesa0 libglx0 libgmp-dev libgmpxx4ldbl libgomp1 libgpgme11 libgpm2 libgprofng0 libgraphite2-3 libgsm1 libgssapi-krb5-2 libgtk-3-0 libgtk-3-bin
  libgtk-3-common libgts-0.7-5 libgts-bin libgudev-1.0-0 libgvc6 libgvpr2 libharfbuzz0b libhdf4-0-alt libhdf5-103-1 libhdf5-hl-100 libheif1 libhtml-form-perl libhtml-format-perl libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl libhtml-tree-perl libhttp-cookies-perl
  libhttp-daemon-perl libhttp-date-perl libhttp-dav-perl libhttp-message-perl libhttp-negotiate-perl libhttp-server-simple-perl libhwasan0 libhwloc-plugins libhwloc15 libi2c0 libibverbs1 libice6 libicu-dev libicu71 libidn12 libimage-exiftool-perl libimagequant0 libiniparser1
  libinput-bin libinput10 libinstpatch-1.0-2 libio-html-perl libio-multiplex-perl libio-socket-inet6-perl libio-socket-ssl-perl libip4tc2 libip6tc2 libipc-shareable-perl libisl23 libitm1 libiw30 libjack-jackd2-0 libjansson4 libjbig0 libjemalloc2 libjpeg-turbo-progs libjpeg62-turbo
  libjs-jquery libjs-jquery-ui libjs-skeleton libjs-sphinxdoc libjs-underscore libjson-c5 libjson-perl libjson-xs-perl libjudydebian1 libk5crypto3 libkeyutils1 libklibc libkmlbase1 libkmldom1 libkmlengine1 libkmod2 libkrb5-3 libkrb5support0 libksba8 liblab-gamut1 liblapack3
  liblbfgsb0 liblcms2-2 libldap-2.5-0 libldap-common libldb2 liblerc3 liblinear4 liblist-moreutils-perl liblist-moreutils-xs-perl libllvm13 libllvm14 liblmdb0 liblocale-gettext-perl liblsan0 libltdl7 liblua5.2-0 liblua5.3-0 libluajit-5.1-2 libluajit-5.1-common liblwp-mediatypes-perl
  liblwp-protocol-https-perl liblz4-dev liblzo2-2 libmagic-dev libmagic-mgc libmagic1 libmailtools-perl libmariadb3 libmath-random-isaac-perl libmath-random-isaac-xs-perl libmaxminddb0 libmd0 libmd4c0 libmemcached11 libmime-charset-perl libminizip1 libmnl0 libmodplug1 libmongoc-1.0-0
  libmongocrypt0 libmotif-common libmp3lame0 libmpc3 libmpdec3 libmpfr6 libmpg123-0 libmtdev1 libncurses-dev libncurses5 libncurses6 libncursesw6 libndctl6 libneon27-gnutls libnet-cidr-perl libnet-dns-perl libnet-dns-sec-perl libnet-http-perl libnet-ip-perl libnet-libidn-perl
  libnet-netmask-perl libnet-server-perl libnet-smtp-ssl-perl libnet-snmp-perl libnet-ssleay-perl libnet-whois-ip-perl libnet1 libnetcdf19 libnetfilter-conntrack3 libnetfilter-queue1 libnewt0.52 libnfnetlink0 libnfsidmap1 libnftables1 libnftnl11 libnghttp2-14 libnginx-mod-http-geoip
  libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream libnginx-mod-stream-geoip libnl-3-200 libnl-genl-3-200 libnl-route-3-200 libnm0 libnpth0 libnsl-dev libnsl2 libnspr4 libnss-systemd libnss3 libntfs-3g89 libnuma1
  libnumber-bytes-human-perl libobjc-11-dev libobjc4 libodbc2 libodbcinst2 libogdi4.1 libogg0 libopenal-data libopenal1 libopengl0 libopenjp2-7 libopus0 libopusfile0 libout123-0 libpam-cap libpam-systemd libpango-1.0-0 libpangocairo-1.0-0 libpangoft2-1.0-0 libparted2 libpathplan4
  libpcap0.8 libpci3 libpciaccess0 libpcre2-16-0 libpcsclite1 libperl4-corelibs-perl libperl5.34 libpfm4 libpipeline1 libpixman-1-0 libpkcs11-helper1 libpmem1 libpng16-16 libpocl2 libpocl2-common libpod-parser-perl libpolkit-agent-1-0 libpolkit-gobject-1-0 libpoppler118 libpopt0
  libportaudio2 libportmidi0 libpq5 libprocps8 libproj25 libprotobuf-c1 libprotobuf23 libproxychains4 libpsl5 libpulse0 libpython2-stdlib libpython2.7-minimal libpython2.7-stdlib libpython3-dev libpython3-stdlib libpython3.10 libpython3.10-dev libpython3.10-minimal
  libpython3.10-stdlib libqhull-r8.0 libqt5core5a libqt5dbus5 libqt5designer5 libqt5gui5 libqt5help5 libqt5network5 libqt5opengl5 libqt5printsupport5 libqt5sql5 libqt5sql5-sqlite libqt5svg5 libqt5test5 libqt5widgets5 libqt5xml5 libradare2-5.0.0 libradare2-common libradare2-dev
  librados2 libraqm0 librdmacm1 libreadline8 libregexp-assemble-perl libregexp-ipv6-perl librsvg2-2 librsvg2-common librtlsdr0 librtmp1 librttopo1 libruby3.0 libsamplerate0 libsasl2-2 libsasl2-modules libsasl2-modules-db libsbc1 libsdl2-2.0-0 libsdl2-image-2.0-0 libsdl2-mixer-2.0-0
  libsdl2-ttf-2.0-0 libsensors-config libsensors5 libserf-1-1 libshine3 libsigsegv2 libslang2 libsm6 libsmbclient libsmi2ldbl libsnappy1v5 libsndfile1 libsndio7.0 libsnmp-base libsnmp40 libsocket6-perl libsodium23 libsombok3 libsoxr0 libspandsp2 libspatialite7 libspeex1 libspeexdsp1
  libsqlite3-0 libssh-4 libssh-gcrypt-4 libssh2-1 libssl1.1 libssl3 libstdc++-11-dev libstring-crc32-perl libstring-random-perl libsuperlu5 libsvn1 libsvtav1enc0 libswresample4 libswscale6 libsybdb5 libsyn123-0 libsystemd-shared libsz2 libtalloc2 libtcl8.6 libtdb1
  libterm-readkey-perl libtevent0 libthai-data libthai0 libtheora0 libtiff5 libtimedate-perl libtinfo-dev libtinfo5 libtirpc-common libtirpc-dev libtirpc3 libtk8.6 libtommath1 libtry-tiny-perl libtsan0 libtsk19 libturbojpeg0 libtwolame0 libtypes-serialiser-perl libubertooth1
  libubsan1 libuchardet0 libucl1 libunicode-linebreak-perl libunsafessl1.0.2 liburcu8 liburi-perl liburing2 liburiparser1 libusb-1.0-0 libutempter0 libutf8proc2 libuv1 libuv1-dev libva-drm2 libva-x11-2 libva2 libvdpau-va-gl1 libvdpau1 libvhdi1 libvmdk1 libvorbis0a libvorbisenc2
  libvorbisfile3 libvpx7 libvulkan1 libwacom-bin libwacom-common libwacom9 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libwbclient0 libwebp7 libwebpdemux2 libwebpmux3 libwebsockets16 libwinpr2-2 libwireshark-data libwireshark15 libwiretap12 libwrap0
  libwsutil13 libwww-mechanize-perl libwww-perl libwww-robotrules-perl libx11-6 libx11-data libx11-xcb1 libx264-164 libx265-199 libxau6 libxaw7 libxcb-dri2-0 libxcb-dri3-0 libxcb-glx0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-present0 libxcb-randr0 libxcb-render-util0
  libxcb-render0 libxcb-shape0 libxcb-shm0 libxcb-sync1 libxcb-util1 libxcb-xfixes0 libxcb-xinerama0 libxcb-xinput0 libxcb-xkb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxerces-c3.2 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon-x11-0 libxkbcommon0
  libxkbfile1 libxm4 libxml-dom-perl libxml-parser-perl libxml-perl libxml-regexp-perl libxml-writer-perl libxml2 libxml2-dev libxml2-utils libxmu6 libxmuu1 libxnvctrl0 libxpm4 libxrandr2 libxrender1 libxshmfence1 libxslt1.1 libxss1 libxt6 libxtables12 libxtst6 libxv1 libxvidcore4
  libxxf86dga1 libxxf86vm1 libyaml-0-2 libyara9 libz3-4 libz3-dev libzip-dev libzip4 libzvbi-common libzvbi0 linux-base linux-libc-dev llvm-13 llvm-13-dev llvm-13-linker-tools llvm-13-runtime llvm-13-tools locales logrotate lrzsz lsb-release lsof lua-lpeg macchanger magicrescue
  mailcap make manpages manpages-dev mariadb-client-10.6 mariadb-client-core-10.6 mariadb-common mariadb-server-10.6 mariadb-server-core-10.6 maskprocessor masscan media-types mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers metasploit-framework mime-support mimikatz
  mingw-w64-common mingw-w64-i686-dev mingw-w64-x86-64-dev minicom miredo mitmproxy mpg123 msfpc mtd-utils multimac mysql-common nasm nbtscan ncompress ncrack ncurses-hexedit ncurses-term net-tools netbase netcat-traditional netdiscover netmask netsed netsniff-ng nfs-common nftables
  nginx nginx-common nginx-core ngrep nikto nmap nmap-common node-normalize.css ntfs-3g ntpsec ocl-icd-libopencl1 offsec-awae-python2 onesixtyone openjdk-11-jre openjdk-11-jre-headless opensc opensc-pkcs11 openssh-client openssh-server openssh-sftp-server openssl openvpn p7zip
  p7zip-full parted passing-the-hash patator patch pci.ids pcscd pdf-parser pdfid perl perl-modules-5.34 perl-openssl-defaults php php-common php-mysql php8.1 php8.1-cli php8.1-common php8.1-mysql php8.1-opcache php8.1-readline pinentry-curses pipal pixiewps pkexec plocate
  pocl-opencl-icd polenum policykit-1 polkitd poppler-data postgresql postgresql-14 postgresql-client-14 postgresql-client-common postgresql-common powershell-empire powersploit procps proj-bin proj-data proxychains4 proxytunnel psmisc ptunnel publicsuffix pwnat pyqt5-dev-tools
  python-apt-common python-babel-localedata python-cffi python-cffi-backend python-is-python3 python-matplotlib-data python2 python2-minimal python2.7 python2.7-minimal python3 python3-adblockparser python3-aiocmd python3-aioconsole python3-aiodns python3-aiofiles python3-aiohttp
  python3-aiomultiprocess python3-aiosignal python3-aiosmb python3-aiosqlite python3-aiowinreg python3-ajpy python3-altgraph python3-aniso8601 python3-anyio python3-appdirs python3-apt python3-asciitree python3-asgiref python3-asn1crypto python3-async-timeout python3-asysocks
  python3-attr python3-automat python3-babel python3-backcall python3-backoff python3-bcrypt python3-bidict python3-binwalk python3-blinker python3-bluepy python3-brotli python3-bs4 python3-censys python3-certifi python3-cffi-backend python3-chardet python3-charset-normalizer
  python3-cheroot python3-cherrypy-cors python3-cherrypy3 python3-click python3-click-plugins python3-colorama python3-commonmark python3-constantly python3-cryptography python3-cycler python3-dataclasses-json python3-dateutil python3-decorator python3-dev python3-dicttoxml
  python3-distlib python3-distutils python3-dnslib python3-dnspython python3-docopt python3-docx python3-donut python3-dotenv python3-dropbox python3-dsinternals python3-engineio python3-et-xmlfile python3-exifread python3-fastapi python3-filelock python3-flasgger python3-flask
  python3-flask-restful python3-flask-socketio python3-fonttools python3-frozenlist python3-fs python3-future python3-gdal python3-gexf python3-gpg python3-greenlet python3-h11 python3-h2 python3-hamcrest python3-hpack python3-html5lib python3-httpagentparser python3-humanize
  python3-hyperframe python3-hyperlink python3-idna python3-impacket python3-importlib-metadata python3-incremental python3-iniconfig python3-invoke python3-ipwhois python3-ipy python3-ipython python3-itsdangerous python3-jaraco.classes python3-jaraco.collections
  python3-jaraco.context python3-jaraco.functools python3-jaraco.text python3-jdcal python3-jedi python3-jinja2 python3-jq python3-jsonschema python3-kaitaistruct python3-kismetcapturebtgeiger python3-kismetcapturefreaklabszigbee python3-kismetcapturertl433
  python3-kismetcapturertladsb python3-kismetcapturertlamr python3-kiwisolver python3-ldap3 python3-ldapdomaindump python3-ldb python3-lib2to3 python3-limiter python3-limits python3-lsassy python3-lxml python3-lz4 python3-macholib python3-magic python3-mako python3-markdown
  python3-markupsafe python3-marshmallow python3-marshmallow-enum python3-matplotlib python3-matplotlib-inline python3-mechanize python3-minidump python3-minikerberos python3-minimal python3-mistune0 python3-more-itertools python3-mpmath python3-msgpack python3-msldap
  python3-multidict python3-mypy-extensions python3-mysqldb python3-nacl python3-neo4j python3-neobolt python3-neotime python3-netaddr python3-netifaces python3-networkx python3-newt python3-ntlm-auth python3-ntp python3-numpy python3-olefile python3-opengl python3-openpyxl
  python3-openssl python3-packaging python3-paramiko python3-parso python3-passlib python3-pcapy python3-pefile python3-pexpect python3-phonenumbers python3-pickleshare python3-pil python3-pil.imagetk python3-pip python3-pip-whl python3-pkg-resources python3-platformdirs
  python3-pluggy python3-pluginbase python3-ply python3-portend python3-pptx python3-prettytable python3-prompt-toolkit python3-protobuf python3-psycopg2 python3-ptyprocess python3-publicsuffix2 python3-publicsuffixlist python3-py python3-pyasn1 python3-pyasn1-modules python3-pycares
  python3-pycryptodome python3-pycurl python3-pydantic python3-pydispatch python3-pydot python3-pyee python3-pygame python3-pygments python3-pygraphviz python3-pyinotify python3-pyinstaller python3-pylnk3 python3-pyminifier python3-pymssql python3-pymysql python3-pyparsing
  python3-pypdf2 python3-pyperclip python3-pyppeteer python3-pypsrp python3-pypykatz python3-pyqt5 python3-pyqt5.qtopengl python3-pyqt5.sip python3-pyqtgraph python3-pyrsistent python3-pysmi python3-pysnmp4 python3-pytest python3-pyvnc python3-pywerview python3-qrcode python3-redis
  python3-repoze.lru python3-requests python3-requests-ntlm python3-requests-toolbelt python3-responses python3-retrying python3-rich python3-routes python3-rq python3-ruamel.yaml python3-ruamel.yaml.clib python3-samba python3-scapy python3-scipy python3-secure python3-serial
  python3-service-identity python3-setuptools python3-setuptools-whl python3-shodan python3-simplejson python3-six python3-slowapi python3-sniffio python3-socketio python3-socks python3-sortedcontainers python3-soupsieve python3-spnego python3-spyse python3-sqlalchemy
  python3-sqlalchemy-ext python3-sqlalchemy-utc python3-starlette python3-stone python3-sympy python3-talloc python3-tdb python3-tempora python3-termcolor python3-terminaltables python3-texttable python3-tk python3-token-bucket python3-tomli python3-tornado python3-tqdm
  python3-traitlets python3-twisted python3-typing-extensions python3-typing-inspect python3-tz python3-ufolib2 python3-ujson python3-unicodecsv python3-unicodedata2 python3-urllib3 python3-urwid python3-uvicorn python3-uvloop python3-virtualenv python3-wcwidth python3-webencodings
  python3-webob python3-websocket python3-websockets python3-websockify python3-werkzeug python3-wheel python3-wheel-whl python3-whois python3-winacl python3-wsproto python3-xlrd python3-xlsxwriter python3-xlutils python3-xlwt python3-xmltodict python3-yaml python3-yara python3-yarl
  python3-zc.lockfile python3-zipp python3-zlib-wrapper python3-zope.interface python3.10 python3.10-dev python3.10-minimal qsslcaudit qt5-gtk-platformtheme qtbase5-dev-tools qtchooser qttranslations5-l10n racc radare2 rake read-edid readline-common reaver rebind recon-ng redsocks
  responder rfkill rpcbind rpcsvc-proto rsmangler rsync ruby ruby-activesupport ruby-addressable ruby-builder ruby-bundler ruby-cms-scanner ruby-concurrent ruby-dev ruby-domain-name ruby-erubi ruby-ethon ruby-ffi ruby-get-process-mem ruby-gssapi ruby-gyoku ruby-http-cookie
  ruby-httpclient ruby-i18n ruby-ipaddress ruby-little-plugger ruby-logging ruby-mime ruby-mime-types ruby-mime-types-data ruby-mini-exiftool ruby-mini-portile2 ruby-multi-json ruby-net-http-digest-auth ruby-net-telnet ruby-nokogiri ruby-nori ruby-ntlm ruby-oj
  ruby-opt-parse-validator ruby-pkg-config ruby-progressbar ruby-public-suffix ruby-rchardet ruby-rubygems ruby-snmp ruby-spider ruby-sqlite3 ruby-typhoeus ruby-tzinfo ruby-unf ruby-unf-ext ruby-webrick ruby-winrm ruby-winrm-fs ruby-xmlrpc ruby-yajl ruby-zeitwerk ruby-zip ruby3.0
  ruby3.0-dev ruby3.0-doc rubygems-integration runit-helper sakis3g samba samba-common samba-common-bin samba-dsdb-modules samba-libs samba-vfs-modules samdump2 sbd scalpel screen scrounge-ntfs sendemail sensible-utils set shared-mime-info skipfish sleuthkit smbclient smbmap snmp
  snmpcheck snmpd socat sphinx-rtd-theme-common spiderfoot spike spooftooph sqlite3 sqlmap sqsh squashfs-tools ssl-cert ssldump sslh sslscan sslsplit statsprocessor stunnel4 sudo swaks sysstat systemd systemd-sysv tasksel tasksel-data tcl-expect tcl8.6 tcpdump tcpick tcpreplay
  tdb-tools telnet testdisk tftp thc-ipv6 thc-pptp-bruter theharvester timgm6mb-soundfont tk8.6-blt2.5 tmux tnftp traceroute tree tshark ucf udev udptunnel unicode-data unicorn-magic unix-privesc-check unixodbc-common unrar unzip update-inetd upx-ucl usbutils va-driver-all
  vboot-kernel-utils vboot-utils vdpau-driver-all vim vim-common vim-runtime vim-tiny vlan voiphopper vpnc vpnc-scripts wafw00f wce webshells weevely wfuzz wget whatweb whois wifite windows-binaries winexe wireless-regdb wireless-tools wireshark-common wordlists wpscan x11-common
  x11-utils xauth xdg-user-dirs xkb-data xxd xz-utils zip zlib1g-dev zsh zsh-autosuggestions zsh-common zsh-syntax-highlighting zstd
The following packages will be upgraded:
  gcc-12-base libc6 libgcc-s1 libstdc++6 perl-base
5 upgraded, 1540 newly installed, 0 to remove and 4 not upgraded.
Need to get 1349 MB of archives.
After this operation, 5762 MB of additional disk space will be used.
Do you want to continue? [Y/n]


```

Installing all the tools takes under 10 mins on a reasonable Internet connection.

## Quirks with the setup

As of the writing of this post, I've been using this setup and workflow without any significant issues. It is likely not a scenario others may find appealing for running Linux on Apple laptops, but as I mention above this fits my needs fine. 

The only thing I've found to be an issue sometimes is terminal emulation quirks. My typical setup is to use [iTerm2](https://iterm2.com/index.html) with [tmux](https://github.com/tmux/tmux/wiki) and so attaching to the Docker container means I am using a console and not a login shell into Kali Linux. Software that has reliance on the terminal type (e.g. ncurses) can sometimes be a bit flaky. It doesn't cause me too much grief as I don't tend to use TUI based software too much.

## Things to look into at some stage

I'm quite enamored with Multipass and will look to using this for [MicroK8s](https://microk8s.io) at some stage. The [installation steps for MicroK8s](https://microk8s.io/docs/install-alternatives) suggest it natively uses Multipass so that makes things easier. The choice of MicroK8s over other micro Kubernetes alternatives such as k0s or k3s for development use is something [@codecowboy](https://codecowboy.io/) covers in a [series of posts](https://codecowboy.io/kubernetes/).

I may look into mounting a local file system from MacOS for use in the Kali Linux container. At this stage there is not much I need from the local filesystem and the information inside the container is fairly transient. I am also a bit cautious with mounting MacOS into other operating systems as I am a very heavy user of metadata and tagging. I had previously found that files with all my MacOS tags all got removed when I mounted them into another operating system as they are MacOS specific.

Example of the metadata included with a file on MacOS:

```Bash

#  mdls docker
_kMDItemDisplayNameWithExtensions  = "docker"
kMDItemContentCreationDate         = 2022-07-29 22:30:24 +0000
kMDItemContentCreationDate_Ranking = 2022-07-29 00:00:00 +0000
kMDItemContentModificationDate     = 2022-07-29 22:30:24 +0000
kMDItemContentType                 = "public.unix-executable"
kMDItemContentTypeTree             = (
    "public.unix-executable",
    "public.data",
    "public.item",
    "public.executable"
)
kMDItemDateAdded                   = 2022-07-29 22:30:24 +0000
kMDItemDisplayName                 = "docker"
kMDItemDocumentIdentifier          = 0
kMDItemFSContentChangeDate         = 2022-07-29 22:30:24 +0000
kMDItemFSCreationDate              = 2022-07-29 22:30:24 +0000
kMDItemFSCreatorCode               = ""
kMDItemFSFinderFlags               = 0
kMDItemFSHasCustomIcon             = (null)
kMDItemFSInvisible                 = 0
kMDItemFSIsExtensionHidden         = 0
kMDItemFSIsStationery              = (null)
kMDItemFSLabel                     = 0
kMDItemFSName                      = "docker"
kMDItemFSNodeCount                 = (null)
kMDItemFSOwnerGroupID              = 20
kMDItemFSOwnerUserID               = 501
kMDItemFSSize                      = 97
kMDItemFSTypeCode                  = ""
kMDItemInterestingDate_Ranking     = 2022-07-29 00:00:00 +0000
kMDItemKind                        = "Unix Executable File"
kMDItemLogicalSize                 = 97
kMDItemPhysicalSize                = 4096
kMDItemSupportFileType             = (
    MDSystemFile
)


```

I also haven't as yet played with creating cloud-init scripts to instantiate the Multipass images. I recently found an [extremely useful example](https://github.com/magnetikonline/macos-multipass-docker) that I may look into integrating into my setup.