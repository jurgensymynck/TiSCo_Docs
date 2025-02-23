
# TiSCo Host Installation and Configuration

**Author:** Jurgen Symynck, making heavy use of Chatgpt 4o.

**Version:** v2.90

**Last Updated:** 2025-02-22

**Changelog**

v2.90   : Added final steps to make TiSCo host remotely accessible.

          This file Assumes that Ubuntu Desktop with Gnome is installed. It uses a mix of Ubuntu server and Ubuntu Desktop installation steps. The desktop and GNOME gui is not needed for regular tisco use,
          Now that the remote access problems are fixed, v3.0 and later doc versions will be based on Ubuntu Server.

**License:** [MIT License](https://opensource.org/licenses/MIT).

Permission is hereby granted, free of charge, to any person obtaining a copy of this document to deal in the document without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies, subject to the following condition:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the document

---

## Table Of Contents

- [Table Of Contents](#table-of-contents)
- [Introduction](#introduction)
- [TiSCo Host System Requirements](#tisco-host-system-requirements)
- [Configuration, Quick overview](#configuration-quick-overview)
- [Clean install of host (`tisco-yellow`) and first boot](#clean-install-of-host-tisco-yellow-and-first-boot)
- [Update and upgrade host](#update-and-upgrade-host)
- [Identify Host IP addres(-ses)](#identify-host-ip-addres-ses)
- [Get `openssh-server` up and running](#get-openssh-server-up-and-running)
- [Add extra keyboard layouts like Dvorak (optional)](#add-extra-keyboard-layouts-like-dvorak-optional)
- [Install extra packages](#install-extra-packages)
- [Install Docker straight from the Docker repository](#install-docker-straight-from-the-docker-repository)
  - [Linux post-installation steps for Docker Engine](#linux-post-installation-steps-for-docker-engine)
- [Clone TiSCo Host using Clonezilla (Optional)](#clone-tisco-host-using-clonezilla-optional)
  - [Preparing the original host](#preparing-the-original-host)
  - [Cloning using Clonezilla](#cloning-using-clonezilla)
  - [Restoring a Clonezilla image on a new host (if applicable)](#restoring-a-clonezilla-image-on-a-new-host-if-applicable)
  - [Post cloning steps on the new host (if applicable)](#post-cloning-steps-on-the-new-host-if-applicable)
- [Identifying and testing connected Adafruit/Arduino boards](#identifying-and-testing-connected-adafruitarduino-boards)
  - [Test for any serial output (`stty`, `cat`, `hexdump`)](#test-for-any-serial-output-stty-cat-hexdump)
  - [Troubleshooting](#troubleshooting)
- [Install OpenC3 COSMOS](#install-openc3-cosmos)
- [Network setup](#network-setup)
- [File sharing](#file-sharing)
- [Setting up Remote Desktop for GNOME](#setting-up-remote-desktop-for-gnome)
- [Set user privileges](#set-user-privileges)
- [TODO](#todo)
- [Setting up status e-mail and other means of Outward communication](#setting-up-status-e-mail-and-other-means-of-outward-communication)
  - [Status email report, contents](#status-email-report-contents)
- [Troubleshooting](#troubleshooting-1)
  - [220/02/2025](#220022025)
- [Add Wi-Fi adapter support](#add-wi-fi-adapter-support)

## Introduction

|               | **Ubuntu Desktop**                                              | **Ubuntu Server**                                           |
|---------------|-----------------------------------------------------------------|-------------------------------------------------------------|
| **Pro's**     | - OpenC3 runs locally via `localhost:2900` and works well after proper installation. <br> - Remote access to OpenC3 via Remote Desktop Protocol (RDP). | - Easy connection to the TiSCo server via IP and port in any browser once the GUI is set up. |
| **Con's**     | - Higher CPU load and more background services than Server edition. <br> - Some settings (e.g., `openssh-server`) require command-line configuration despite GUI availability. | - GUI setup is complex due to dependency installation and user permission configurations. |

## TiSCo Host System Requirements

  1. See [OpenC3 COSMOS requirements](https://docs.openc3.com/docs/guides/performance).
  2. See `Docker` system requirements.
  3. Lots of USB ports For Wi-fi adapters, Bluetooth adapters, several Adafruit / Arduino boards, And at least one usb button powered by Arduino Trinket M0 board.

## Configuration, Quick overview

1. Choose Ubuntu Server or Ubuntu Desktop, English language, Long term release. If choosing desktop, Choose Gnome, minimal install.
2. Default user: tisco. choose simple password that is quick to type. For simplicity, use this configuration for all user and password combinations needed.
3. Get openssh-server up and running as soon as possible for copy-pasting from this document to the host.
4. Install a bunch of extra tools for better user experience Configuration and debugging.
5. Install Docker straight from the Docker repo.
6. Install OpenC3 Cosmos.

    At the time of writing, Cosmos will not work Out of the box. See extra modifications of `compose.yaml` to addresses the problems.

3. Hosts should run Docker and OpenC3 containers after each boot.
7. Configure all user rights and permissions for running Docker, OpenC3, access to serial ports, access to USB devices, access to regular and Wi-Fi adapters, ...
8. If everything works, using Clonzilla, take snapshot of the state of the TiSCo host.
9. Configure the necessary tool to send network and device status, after booting and when the special USB front panel button is pressed
   1. Configure and install the necessary tools for the usb button, as a HID device.
   2. Send the data by email, by Wi-fi broadcasting, by Bluetooth adapter protocol, by ethernet crossover cable, ...

## Clean install of host (`tisco-yellow`) and first boot

1. From <https://ubuntu.com/>, download Ubuntu Desktop LTS
<!-- 2. From <https://ubuntu.com/>, domnload Ubuntu server LTS
   (ubuntu-24.04.1-live-server-amd64.iso) or later LTS Version -->
1. Using Balena Etcher or alternative, burn iso file on USB flash drive.
2. plug in USB flash drive, connect keyboard, mouse and display.
3. Connect host using (wired) ethernet cable to the internet.
4. Boot host, press F12 or other neccessary key to enter boot options.
5. Set correct date and time.
6. Boot using "USB Storage Device". Start installation of Ubuntu.
7. Install support for the connected keyboard.
8. Choose *Use complete disk*, accept suggested disk partitioning.
9. Choose *Install minimum version*.
10. Choose *install extra proprietary drivers*.
11. Choose *log in Gnome without authentication*.
<!-- 8.  Installation base: *Ubuntu Server* (default)
   enable *Search for Third-party drivers*.
1.  network config: Auto config should have found working ethernet connection.
2.  Ubuntu archive mirror config: accept the proposed mirror
3.  Guided storage configuration: Accept all defaults and press *Done*
    Accept the default *Storage configuration* and press *Done*, *Continue*
    Profile configuration:
    your name: *Your real name*
    Your servers name: *tisco-yellow* or as desired.
    User name: *tisco*, all lower case!
    Password: as desired.
4.  skip upgrading to Ubuntu pro. -->
<!-- 14. : *install open ssh server* and *Allow password authentication over Ssh*. do not import do not import  SSH keys. -->

12. Updating System ...

    After a few minutes, **reboot now**, remove the installation medium when prompted.
13. After booting: no need to do anything in Gnome for now: choose "Log off".
14. Switch to a virtual console with the keyboard combination  `Ctrl` + `Alt` + `F3` ... `F6`
15. Log in using tisco credentials.

## Update and upgrade host

Before any installation is done, upgrade the packet manager:

```bash
sudo apt update
sudo apt upgrade
```

## Identify Host IP addres(-ses)

Identify the IP address of the hosts ethernet network adapter. On the host, run any of the following commands:

```bash
ip a
ip addr show
hostname -I

# nmcli (Network Manager) must already been installed for lower code to work:
nmcli device show
```

Note that in real life, finding out the IP address of the host is a major undertaking when there are no display and keyboard attached.

## Get `openssh-server` up and running

Get an `ssh` connection to the host up and running as soon as possible because this allows for copy/pasting code snippets from this document into the remote terminal.

1. Install `openssh-server` and ensure the SSH server is running:

    ```bash
    sudo apt install openssh-server

    sudo systemctl status ssh
    sudo systemctl start ssh
    sudo systemctl enable ssh
    ```

2. Configure `ssh` settings:

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

    ```sshconfig
    # Port 2222             # optional
    # AllowUsers tisco      # optional
    PermitRootLogin no
    PubkeyAuthentication yes
    ```

    There should be no firewall running, but if there is, allow `ssh` through (not explained here).

    ```bash
    sudo ufw status
    ```

3. Apply changes:

    ```bash
    sudo systemctl restart ssh
    ```

4. **Reboot the host**

5. On a *client machine*, in a terminal, try connecting to the host:

    ```bash
    ssh tisco@192.168.0.165     # Replace with the host ip address
    ```

    When `openssh-server` is up and actively listening, you get something like this (redacted):

    ```text
    The authenticity of host '192.168.0.165 (192.168.0.165)' can't be established.
    XXXXXXX key fingerprint is SHA256:*******************************************.
    This key is not known by any other names.
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
    ```

    Enter `yes` to add the ip address to the list of known hosts.

   If response is:

    ```text
    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
    Host key for 192.168.0.165 has changed.
    Host key verification failed.
    ```

    ... then likely the Wi-Fi adapter on this host was used on *another* host. It seems that the generated  IP addresses are linked to a specific Wi-Fi adapter.

    The warning is expected behavior because all TiSCo hosts may be outfitted with any of the provided USB Wi-Fi adapters.

    You can remove the host key of known host:

    ```bash
    ssh-keygen -R 192.168.0.165
    ```

    ... and try connecting again.

## Add extra keyboard layouts like Dvorak (optional)

1. If needed, install extra keyboard layouts like `dvorak` alongside the default (here: Belgian):

    ```bash
    sudo apt install console-setup keyboard-configuration
    sudo nano /etc/default/keyboard
    ```

    ```conf
    # KEYBOARD CONFIGURATION FILE
    XKBMODEL="pc105"
    XKBLAYOUT="be,us"
    XKBVARIANT=",dvorak"
    XKBOPTIONS="grp:alt_shift_toggle" # Enables layout switching with Alt + Shift.
    ```

2. Apply the keyboard configuration:

    ```bash
    sudo dpkg-reconfigure keyboard-configuration
    sudo systemctl restart keyboard-setup
    sudo systemctl restart console-setup
    ```

3. Ensure the new layout persists after reboot:

    ```bash
    sudo update-initramfs -u
    ```

## Install extra packages

All following commands can be run physically on the host or using a remote `ssh` unless specified otherwise.

1. First, update and upgrade the linux host if not already done so:

    ```bash
    sudo apt update
    sudo apt upgrade
    ```

2. Install the extra packages:

    ```bash
    sudo apt install network-manager git python3 nmap htop postfix sensors screen mc micro w3m lynx links wget gpm grc ccze beep bsdmainutils
    ```

    After installing these you can run `apropos -e -w`  to get short descriptions (Optional):

    ```bash
    apropos -e -w network-manager git python3 nmap htop postfix sensors screen mc micro w3m lynx links wget gpm grc ccze beep bsdmainutils
    ```

3. Cleanup:

    ```bash
    sudo apt autoremove
    ```

## Install Docker straight from the Docker repository

(source: <https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository>)

1. Add Docker's official GPG key:

    ```bash
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    ```

2. Add the repository to Apt sources:

    ```bash
    echo \
    "deb [arch=$(dpkg --print-architecture) \
    signed-by=/etc/apt/keyrings/docker.asc] \
    https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && \
    echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") \
    stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > \
    /dev/null

    sudo apt-get update
    ```

3. Install the latest version:

    ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

4. Verify that the installation is successful by running the `hello-world` image:

    ```bash
    sudo docker run hello-world
    ```

### Linux post-installation steps for Docker Engine

<https://docs.docker.com/engine/install/linux-postinstall/>

1. Create the docker group:

    ```bash
    sudo groupadd docker
    ```

2. Add your user to the docker group:

    ```bash
    sudo usermod -aG docker tisco
    ```

3. Log out and log back in, or activate the changes to groups:

    ```bash
    newgrp docker
    ```

4. Verify that you can run docker commands *without* sudo:

    ```bash
    docker run hello-world
    ```

5. Configure Docker to start on boot with `systemd`:

    ```bash
    sudo systemctl enable docker.service
    sudo systemctl enable containerd.service
    ```

<!-- 6. Configure default logging driver

    TODO -->

<!-- 1. Set user privileges

        sudo usermod -aG dialout tisco
        newgrp dialout

        sudo usermod -aG plugdev tisco
        newgrp plugdev

        sudo groupadd docker
        sudo usermod -aG docker tisco
        newgrp docker -->

<!-- 2. check group access:

        getent group | grep tisco -->

## Clone TiSCo Host using Clonezilla (Optional)

It is a very good idea at this point to back up your progress by making a clone image using [Clonezilla](https://clonezilla.org/).

You will need a USB thumb drive with a few gigabytes for Clonezilla, And another USB thumb drive to store the clone image.

At the day of writing, the author's Ubuntu Desktop + Docker image was approximate 3 GB.

Clonezilla provides other means of storing the clone image. See its documentation.

### Preparing the original host

To prepare a Linux machine for cloning, it's important to clean up unnecessary files to reduce the image size and ensure the cloned system works efficiently.

1. Shut down Gnome using the command line:

    - Verify that the Active Display Manager is GDM:

        ```bash
        cat /etc/X11/default-display-manager
        ```

    - Stop the Display Manager (shut down the GUI). For GDM (Gnome):

        ```bash
        sudo systemctl stop gdm
        ```

2. Clean package cache:

    ```bash
    sudo apt-get clean
    sudo apt-get autoremove --purge -y
    sudo apt-get autoclean
    ```

3. Remove old kernel versions (If Applicable)

    ```bash
    sudo apt --purge autoremove
    ```

4. Clear system logs

   - Truncate log files:

        ```bash
        sudo find /var/log -type f -exec truncate -s 0 {} \;
        ```

   - Clear journal logs (for systemd systems):

        ```bash
        sudo journalctl --vacuum-time=7d
        ```

5. Remove system-wide temporary files:

    ```bash
    sudo rm -rf /tmp/*
    sudo rm -rf /var/tmp/*
    ```

6. Remove user-specific temporary files:

    ```bash
    rm -rf ~/.cache/*
    ```

7. Clear Bash shell history (Optional):

    ```bash
    history -c
    cat /dev/null > ~/.bash_history

    sudo find /home -name ".bash_history" -exec truncate -s 0 {} \;
    sudo truncate -s 0 /root/.bash_history
    ```

8. Clean SSH Keys, Clear known hosts:

    ```bash
    sudo rm -f /etc/ssh/ssh_host_*
    ```

    Remember to regenerate after cloning with `sudo dpkg-reconfigure openssh-server`!

9. Remove DHCP lease files:

    ```bash
    sudo rm -f /var/lib/dhcp/*
    sudo rm -f /var/lib/NetworkManager/dhclient-*.lease
    ```

10. Clean User Sessions and Locks. Remove user session data:

    ```bash
    sudo rm -rf /run/user/*
    ```

11. Clear stale lock files:

    ```bash
    sudo find /var/lock -type f -delete
    ```

<!-- 11. Remove Swap Files (Optional)
    - Disable swap:

        sudo swapoff -a

    -   Clear swap partition (optional):

        sudo dd if=/dev/zero of=/swapfile bs=1M status=progress

    Remember to Recreate swap after cloning with `sudo mkswap /swapfile` and
`sudo swapon /swapfile` -->

### Cloning using Clonezilla

These steps are fairly straightforward and well documented in the Clonezilla documentation.

Here we use the method *clone a complete disk to an external image*.

### Restoring a Clonezilla image on a new host (if applicable)

Restore the image on a new host machine using Clonezilla.

### Post cloning steps on the new host (if applicable)


On the new host, after booting, with keyboard and display connected:

1. Rename host (TODO)

3. Regenerate SSH Keys:

    ```bash
    sudo dpkg-reconfigure openssh-server
    ```

4. Reboot

5. on the *client*, remove outdated ssh connection settings (see Elsewhere in this document)
6.
    ```bash
    ssh-keygen -R <CLONED-HOST-IP-ADDRESS>
    ```

    ... and try connecting again.




## Identifying and testing connected Adafruit/Arduino boards

This section deals with finding Arduino boards as serial devices but also the Arduino Trinket M0 as Human Interface Device (HID).
1. Plug in one or more boards, if not already done so.

    The following assumes one single *Original Adafruit Grand Central M4 featuring SAMD51P20* board is plugged in.

4. Look for serial devices in the `/dev/` directory.
   Most Linux boards use  `ttyACM*` or `ttyUSB*`.
   The output will list the available serial ports.

    ```bash
        ls /dev/ttyACM*
        ls /dev/ttyUSB*
    ```

5. Use `udevadm` to get detailed information. Use `grep` to filter out the useful parts:

    ```bash
    udevadm info -q all -n /dev/ttyACM* | grep \
        -e 'DEVNAME='  \
        -e 'ID_MODEL=' \
        -e 'ID_MODEL_FROM_DATABASE='  \
        -e 'ID_VENDOR=' \
        -e 'ID_VENDOR_FROM_DATABASE=' \
    ```

    For example, this is the output on the authors TiSCo host. Note the absenge of any `GROUP=` results:

    ```ini
    E: DEVNAME=/dev/ttyACM0
    E: ID_MODEL=Adafruit_Grand_Central_M4
    E: ID_VENDOR=Adafruit
    E: ID_VENDOR_FROM_DATABASE=Adafruit
    ```

6. Set Arduino board device permissions:

    (source: [OpenC3 docs](https://docs.openc3.com/docs/guides/bridges#note-on-serial-ports)).

    Note: This section presents only solutions that are persistent after a host reboot.

    **Assuming a board is connected to `/dev/ttyACM0`**:

    ```bash
    ls -l /dev/ttyACM0
    ```

    ```bash
    crw-rw---- 1 root 166, 0 <MONTH> <DAY> <TIME> /dev/ttyACM0
    ```

    The above privileges are insufficient: The device should be world-writable (read and write access for everyone), and it should be associated with the `dialout` group, to be useable by some applications (e.g., `screen`, `cat`, `minicom`) that may still expect a *user* to be in the `dialout` group.

    Ensure persistent privilege changes for `/dev/ttyACM0` across reboots by creating `udev` rule(s), to automatically set the permissions whenever an ACM* device is connected.

    ```bash
    sudo nano /etc/udev/rules.d/99-myserial.rules
    ```

    ```conf
    KERNEL=="ttyACM[0-9]*", GROUP="dialout", MODE="0666"
    ```

    - `KERNEL=="ttyACM[0-9]*"` Matches any device named /dev/ttyACM0, /dev/ttyACM1, etc.

    - `GROUP="dialout"` Assigns the device to the dialout group, ensuring users in this group have access.

    - `MODE="0666"` → Grants read and write (rw-rw-rw-) permissions to all users, making `GROUP="dialout"` redundant in theory, but some applications (e.g., `screen`, `cat`, `minicom`) may still expect a *user* to be in the `dialout` group.
  -
<!--
TODO: what aboit plugdev? -->

7. Add `tisco` to the `dialout` and `plugdev` group:

    ```bash
    sudo usermod -a -G dialout,plugdev tisco

    ```

    The `plugdev` group allows wounting/dismounting        thumb drives, Arduino boards, Wi-fi and Bluetooth adapters.

    This modification is persistent across reboots of the host.

8. **Reboot the host with the devices attached.**
9. After booting, double check permissions and group access:

    ```bash
    ls -l /dev/ttyACM0
    ```

    result:

    ```conf
    crw-rw-rw- 1 root dialout 166, 0 <MONTH> <DAY> <TIME> /dev/ttyACM0
    ```

    ```bash
    groups
    ```

    result:

    ```conf
    tisco adm *dialout* sudo *plugdev* users docker [...]
    ```

### Test for any serial output (`stty`, `cat`, `hexdump`)

You can preload the Arduino board with the sketch below using modern Arduino IDE:

```cpp
/* Ping simulation sketch */

// TODO: update this packet according to latest TiSCo package definitions.
const uint8_t tlm_ping_packet[] = {
    0x81,  // packet ID
    0x08,  // frame size
    0,     // PADDING
    0,     // PADDING
    0xDE,  // timestamp (32 bits)
    0x30,  //   Here, 'DEMOCODE' is used
    0xC0,  //   to distinguish
    0xDE   //   with production code.
};


void setup() {
    Serial.begin(115200);
}

void loop() {
    Serial.write(tlm_ping_packet, sizeof(tlm_ping_packet));
    delay(10);

    for (uint8_t i = 0; i < 3; ++i) {
        digitalWrite(LED_BUILTIN, HIGH);
        delay(10);
        digitalWrite(LED_BUILTIN, LOW);
        delay(190);
    }

    delay(1390);
}
```

If the boards firmware (or Arduino sketch) should be sending serial data, check by listening to the port.

(**Assuming(!)** the `/dev/ttyACM0` board is sending with matching settings

 1. Check and set serial configuration:

    Run lower code for noninteractive reading of presumed binary data on the serial port.

    Always precede `cat` with the `stty` line, because after each board reset, the serial port settings reset to their defaults:


    ```bash
    stty -F /dev/ttyACM0 115200 raw

    # Check (optional)
    stty -F /dev/ttyACM0

    cat /dev/ttyACM0 | hexdump -Cv
    ```

    - `-F`: `stty` operates on the specified serial device (`/dev/ttyACM0`), instead of the terminal.
    - `8N1`: `8` data bits, `N`o parity, `1` stop bit.
    - `raw`: disable all input and output processing.
    - `-C` (canonical format) ensures:
    - The offset (memory address of each row) is displayed on the left.
    - Hexadecimal values of the bytes are in the center.
    - ASCII representation of printable characters is on the right.
    - `-v` → Prevents suppression of repeated lines.

    Example readout (with 'ping simulation sketch')

    ```hex
    00000000  81 08 00 00 de 30 c0 de  81 08 00 00 de 30 c0 de  |.....0.......0..|
    00000010  81 08 00 00 de 30 c0 de  81 08 00 00 de 30 c0 de  |.....0.......0..|
    00000020  81 08 00 00 de 30 c0 de  81 08 00 00 de 30 c0 de  |.....0.......0..|
    ```

    Example readout (6 real TiSCo `TLM_PING` packets):

    ```hex

    00000000  81 08 00 00 59 ec 03 00  81 08 00 00 eb f6 03 00  |....Y...........|
    00000010  81 08 00 00 7c 01 04 00  81 08 00 00 0d 0c 04 00  |....|...........|
    00000020  81 08 00 00 9e 16 04 00  81 08 00 00 2f 21 04 00  |............/!..|
    ```

    `screen` Should also work, but is intended for interactive sessions:

    ```bash
    screen /dev/ttyACM0 115200
    ```

    **Important**: Terminate a `screen` session with `Crtl-A` + `K` (Kill) to prevent multiple sessions blocking the port.

### Troubleshooting

1. Be sure that the is sending data at the specified baud rate,  check the default settings of `stty` and doublecheck the boards firmware (or Arduino sketch)!

2. If you fumble around with `screen`, `cat`, `hexdump` ... make sure to kill all processes that might block `/dev/ttyACM0`:

    ```bash
    lsof /dev/ttyACM0
    ```

    Might result in, for example:

    ```plain
    COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    screen  3445 tisco    5u   CHR  166,0      0t0 1150 /dev/ttyACM0
    ```

    Kill the blocking proces with (Replace `<PID>` with the actual number):

    ```bash
    sudo kill <PID>
    ```

3. `dmesg` - Kernel messages

   The `dmesg` command shows kernel messages, including information about
   connected devices. After connecting your Linux board:

    ```bash
    sudo dmesg | grep adafruit
    sudo dmesg | grep arduino
    sudo dmesg | grep tty
    ```

4. `lsusb` - display newly plugged USB device

    To pinpoint a new device, `diff` the redirected output of `lsusb` before and after connecting the usb device.

    ```bash
    before=$(mktemp)
    after=$(mktemp)

    lsusb > "$before"

    echo "Please plug in the USB device and press Enter to continue..."
    read

    lsusb > "$after"

    diff "$before" "$after"

    rm "$before" "$after"
    ```










## Install OpenC3 COSMOS

(sources: <https://docs.openc3.com/docs/getting-started/installation>,
 <https://docs.openc3.com/docs/guides/raspberrypi>)

1. Clone the cosmos-project repo into tisco's `~` folder, resulting in a new
`cosmos-project` folder:

    ```bash
    cd ~
    git clone https://github.com/OpenC3/cosmos-project.git
    cd ~/cosmos-project
    ```

2. Prevent the OpenC3 demo from installing:

    ```bash
    nano ~/cosmos-project/.env
    ```

    comment out `OPENC3_DEMO=1`:

    ```conf
    # OPENC3_DEMO=1
    ```
3. Create shared folder `tisco-shared`

    This folder will be used to share client-generated OpenC3 COSMOS plugin gems with the host:

    ```bash
    mkdir ~/tisco-shared
    ```

4. Add `~/cosmos-project` and `~/tisco-shared` to the system path.

    ```bash
    nano ~/.profile
    ```

    ```conf
    # add to end of the file
    PATH="$HOME/cosmos-project:$HOME/tisco-shared:$PATH"
    ```

**The following assumes that an Adafruit/Arduino board is connected to `/dev/ttyACM0`!**

5. Open `compose.yaml` for modification:

    ```bash
    nano ~/cosmos-project/compose.yaml
    ```

6. To prevent this serial connection problem

    (source: <https://github.com/OpenC3/cosmos/issues/57>).:

    ```log
    SERIAL_INT: ENOENT : No such file or directory @ rb_sysopen - /dev/ttyACM0
    ```
    <!--    - Under `openc3-operator:` ->  `volumes:`, add `/dev/ttyACM0:/dev/ttyACM0` and other serial ports.
        - Also  -->

   - Under `openc3-operator:` ->  `volumes:`, add `privileged: true`.

7. To grant remote access to OpenC3 from another client:

    - Under `openc3-traefik:` -> `ports:` remove the `127.0.0.1:` from the port forwarding lines.

    - Under `openc3-traefik:` -> `volumes:` uncomment all lines.


    A `diff` between original and updated `compose.yaml` should look like this:

    ```bash
    diff compose_original.yaml compose.yaml
    ```

    ```diff
    188a189
    >     privileged: true
    196,200c197,201
    <       # - "./openc3-traefik/traefik-allow-http.yaml:/etc/traefik/traefik.yaml:z"
    <       # - "./openc3-traefik/traefik-ssl.yaml:/etc/traefik/traefik.yaml:z"
    <       # - "./openc3-traefik/traefik-letsencrypt.yaml:/etc/traefik/traefik.yaml:z"
    <       # - "./openc3-traefik/cert.key:/etc/traefik/cert.key:z"
    <       # - "./openc3-traefik/cert.crt:/etc/traefik/cert.crt:z"
    ---
    >       - "./openc3-traefik/traefik-allow-http.yaml:/etc/traefik/traefik.yaml:z"
    >       - "./openc3-traefik/traefik-ssl.yaml:/etc/traefik/traefik.yaml:z"
    >       - "./openc3-traefik/traefik-letsencrypt.yaml:/etc/traefik/traefik.yaml:z"
    >       - "./openc3-traefik/cert.key:/etc/traefik/cert.key:z"
    >       - "./openc3-traefik/cert.crt:/etc/traefik/cert.crt:z"
    202,203c203,206
    <       - "127.0.0.1:2900:2900"
    <       - "127.0.0.1:2943:2943"
    ---
    >       # - "127.0.0.1:2900:2900"
    >       # - "127.0.0.1:2943:2943"
    >       - "2900:2900"
    >       - "2943:2943"
    204a208
    >       - "80:2900"
    205a210,211
    >       - "443:2943"
    >
    ```

8. Prevent `SERIAL_INT: EPERM : Operation not permitted @ rb_sysopen - /dev/ttyACM0`
and `SERIAL_INT: EACCES : Permission denied @ rb_sysopen - /dev/ttyACM0`

   ... by setting correct device permissions AND setting persistency over unplugging and resetting the Arduino board. See elsewhere in this document.

1. Install and run OpenC3 COSMOS by running:

    ```bash
    cd ~/cosmos-project
    ./openc3.sh run
    ```

    The first time the above command runs, it may take a while as it retrieves all necessary files from the source.

2.  Build the tisco plugin on the client:
    On the client Docker must be installed as well as Openc3 COSMOS, just as on the host.

    Note: Just for building the plugin, only container `openc3-cosmos-cmd-tlm-api` seems to be needed, which indicates that
    *Docker Desktop* and *Docker Compose* might not be needed (TODO: verify).

3.  Move plugin to shared folder `tisco-shared`:
12.
    The author uses a VSCode plugin called *SSH FS* by Kelvin Schoofs (`kelvin.vscode-sshfs`), to send and receive files over ssh.

    Alternatively one can use SCP protocol (see elsewhere in this document).

<!-- ## Setting/Checking tisco user privileges

Many commands that should be readily accessible for TiSCo require root privileges.
Below are commands to prevent using schedule.

1. Allow tisco to power-off and reboot without authentication
   (but still needing `sudo`): Add tisco to the `sudo` group,
   Apply Changes (Without Reboot), and verify by powering down.

        getent group sudo
        sudo usermod -aG sudo tisco
        newgrp sudo

        # use of sudo remains mandatory.
        # WARNING: will poweroff system immediately!
        sudo systemctl poweroff # reboot
        -->

10. Run `docker` and `docker compose` Without Root Privileges,
   Apply Changes (Without Reboot), and verify.

        sudo usermod -aG docker tisco
        docker compose ps
        newgrp docker

Finally, chech which groups user `tisco` is a member of:

        getent group | grep tisco



<!-- 1. Install the beep command

        sudo apt-get install beep

1. Load the `pcspkr` Kernel Module

        systemd: /etc/modules-load.d/*.conf

1. To ensure it loads automatically at boot, use systemd for managing services and modules (`/etc/modules-load.d/*.conf`):

        sudo nano /etc/modules-load.d/pcspkr.conf

1. Add the module name: `pcspkr`
1. Save the file and exit.
1. Test and verify by reloading the configuration:

        sudo systemctl restart systemd-modules-load.service
        lsmod | grep pcspkr -->

## Network setup

A handful of labeled Asus adapters are provided that have a black on white
four-digit label separated by a ':'. these are the last four digits
of the adapters MAC address.

**Important: The following steps assume that you are logged in on the host, either via an Existing up-and-running `ssh` session, or physically on the machine using dedicated keyboard, mouse and display!**

The Recommended tool for network setup and verification is Network Manager (NM), the background service (daemon) responsible for managing network connections, itself managed by `systemd`.
`nmcli` is a command-line tool that provides an interface to interact with the Network Manager daemon.
`nmtui`  is a a simple, text-based interface for managing network connections in a more interactive, menu-driven manner.

1.

        sudo apt install network-manager

2. Ensure that NetworkManager is running. If not, run second line.

        sudo systemctl status NetworkManager
        sudo systemctl start NetworkManager

3. On Ubuntu Server, by default `netplan` is used. Make the switch to Network manager

        sudo nano /etc/netplan/01-netcfg.yaml

        # preserve indentation!
        network:
          version: 2
          renderer: NetworkManager

        sudo netplan apply
        sudo chown root:root /etc/netplan/01-netcfg.yaml

    It should Be safe to remove anyother existing files in the `/etc/netplan/` folder.

4. Check which network interfaces are available and their statuses:

        nmcli device status

5. To scan for available Wi-Fi networks:

        nmcli device wifi list

**Work in progress**

- in an Ubuntu session, plug in all available USB Wi-Fi adapters and make sure they make connection to an available Wi-Fi network. This ensures that the host will recognize any regular network adapter and USB Wi-Fi adapter that is plugged in.
Doing so will allow custom discovery script that run on a client to discover the tisco hosts. Currently the scriptname used is called `tisco_find_host.py`


## File sharing

For moving the OpenC3 COSMOS plugin Gem that holds the target from the windows development machine to the linux TiSCo Host machine.

Use an SFTP client like WinSCP or FileZilla on the Windows machine.
Connect to the Linux server using its hostname or IP and SSH credentials.

Transfer Files via SCP
How it Works: SCP (Secure Copy Protocol) allows you to transfer files over SSH, using a simple command or client.

1. On the client, check installation of  `scp`

        usage: scp [-346ABCOpqRrsTv] [-c cipher] [-D sftp_server_path]

...

## Setting up Remote Desktop for GNOME
<!-- , from the command line -->

1. Connect keyboard, mouse and display to the host.
2. Log in to a GNOME session if not already logged in.
3. Configure *Remote Desktop* AND *Desktop Sharing* via 'Settings'.
    If *Settings* is not responding, do a software update and reboot. Your next attempt at working with *Settings* might be interrupted with an authentication request to unlock a key ring. After that things will work.

    On the client side, initiating a remote desktop session using the suggested port `3390` is *Desktop Sharing* (recommended). Port `3389` is *Remote Desktop* where any previous `tisco` GNOME sessions must be terminated and a brand new session is started.

**TODO: WIP**

<!-- 1. Check GNOME version by checking shell version:

    ```bash
    gnome-shell --version
    ```

1. Enable Remote Login in GNOME 46, enable the Remote Desktop Service:

    ```bash
    systemctl enable --user --now gnome-remote-desktop
    ```

    This command starts the `gnome-remote-desktop` service for your user and ensures it runs on startup.

    output:

    ```plain
    Created symlink /home/tisco/.config/systemd/user/gnome-session.target.wants/gnome-remote-desktop.service → /usr/lib/systemd/user/gnome-remote-desktop.service.
    ``` -->


## Set user privileges

<!-- 1. Add tisco user to the `dialout` group :

        sudo usermod -aG dialout tisco -->

1. Ensure tisco user has access to the `hidraw` devices. test with:

        ls /dev/hidraw*

    If access granted, a list of HID devices is shown.
    If not, adjust permissions similarly via `udev` (see below)

1. Create a Udev Rule File:

        sudo nano /etc/udev/rules.d/99-adafruit-trinket.rules

1. Add the Following Content:

        # Adafruit Trinket M0 - USB Mass Storage, HID, Serial
        # Backslash (\) Usage: Ensure there are no trailing spaces after the backslash!

        ATTRS{idVendor}=="239a", \
        ATTRS{idProduct}=="801f", \
        MODE:="0666", \
        GROUP="plugdev", \
        ENV{ID_MM_DEVICE_IGNORE}="1", \
        TAG+="uaccess"

1. Apply the Udev Rules:

        sudo udevadm control --reload-rules
        sudo udevadm trigger

## TODO

- prevent long waiting For powering off when pressing Power button.
Also log off without asking when remote users are connected
When clicking power offin Gnome and allowing the counter to go to Does host ask for credentials this should be removed.

- In the X session I have way too many questions for credentials when running remotely
- Allow the option to switch between keyboards when logging on the remote desktop
- Have `htop` Run in a terminal unappropriate size

## Setting up status e-mail and other means of Outward communication
### Status email report, contents

A front panel USB HID (Human interface device) Button, Based on Adafruit Trinkett M0, Will triggersending an email with comprehensive status report.

Its contents:

- Host name, uptime, default user
IP addresses, names and MAC addresses of all physical Wi-fi devices, Leaving out all virtual devices.
- Available network services and ports (using `nmap` Scanning the local host?)
- names and descriptions of all connected USB connected devices (The HID button(s),)
- names and descriptions of all connected Serial devices (all Arduino and Adafruit boards), including Baud Rate and Serial Parameter settings.
- General Troubleshooting Information

  - Logs: Check system logs `/var/log/syslog`, `dmesg`
      for errors related to network or USB connectivity.
  - Service Status: Verify essential services like `ssh`
      networking, or serial daemons are running.


1. Open the `postfix` configuration file:

        sudo nano /etc/postfix/main.cf

2. Set or verify the following options:

        ...
        myhostname          = tisco-red
        myorigin            = $myhostname
        # inet_interfaces   = loopback-only
        inet_interfaces     = all
        ...

3. Restart Postfix to apply changes:

        sudo systemctl restart postfix

<!-- 3. Use `s-nail` to test:

        echo "This is a test email." | s-nail -v -s "Test Email" jurgen.symynck@gmail.com -->

## Troubleshooting

- view docker log files: `journalctl -u docker.service | ccze -A`
- Get OS and kernel info using any of following commands.
  Useful to submit bug reports to OpenC3 COSMOS Github issue tracker.

        lsb_release -a
        cat /etc/os-release
        hostnamectl
        uname -a

### 220/02/2025


## Add Wi-Fi adapter support

**`nmcli` command summary**

```bash
nmcli general status
nmcli device status
nmcli device show
nmcli device wifi list
nmcli connection show

sudo nmcli device wifi \
    connect "telenet-0011811" \
    password |"***************" ifname wlx0c9d92b64ab9
```

1. Insert the USB Wi-Fi adapter and check if it's recognized:

    ```bash
    lsusb
    ip link show
    ```

2. If not already installed, install Network Manager and check status:

    ```bash
    sudo apt install network-manager
    nmcli general status
    ```

3. Check which network interfaces are available and their statuses:

    ```bash
    nmcli device status
    ```

4. To scan for available Wi-Fi networks:

    ```bash
    nmcli device wifi list
    ```

5. Connect to a Wi-Fi Network:

    ```bash
    # for tisco-yellow at home:
    sudo nmcli device wifi connect "*************" password "*************" ifname wlx0c9d92b64ab9
    ```

    <!-- # Replace <SSID> with your Wi-Fi network name.
    # Replace <password> with the actual Wi-Fi password.
    # Replace wlan0 with your Wi-Fi interface name. -->

6. Verify connection by checking connection status:

    ```bash
    nmcli device status
    nmcli connection show
    ip a
    ping -c 4 8.8.8.8
    ```

7. Ensure auto-connect at boot:

    ```bash
    sudo nmcli connection modify "telenet-0011811" connection.autoconnect yes
    ```

8. Universal Network-manager auto-connect configuration: enable auto-connect for all interfaces:

    ```bash
    sudo nmcli networking on
    sudo nmcli radio wifi on
    ```

9. Create a universal Wi-Fi connection profile:

    This profile should dynamically attach to any matching Wi-Fi adapter without binding to a specific interface. The real mac addresses will be used.

    ```bash
    sudo nmcli connection add type wifi ifname "*" \
    con-name "universal-wifi" \
    ssid "*************" \
    wifi-sec.key-mgmt wpa-psk \
    wifi-sec.psk "*************" \
    autoconnect yes \
    802-11-wireless.cloned-mac-address permanent
    ```

10. Prevent MAC randomization:

    Apply a system-wide policy (global setting) for preventing any connection from using MAC randomization across all interfaces:

    We use the MAC addresses to identify a proper TiSCo host.

    ```bash
    sudo nano /etc/NetworkManager/NetworkManager.conf
    ```

    ```conf
    # /etc/NetworkManager/NetworkManager.conf
    [device]
    wifi.scan-rand-mac-address=no

    [connection]
    wifi.cloned-mac-address=permanent
    ```

11. Restart NetworkManager:

    ```bash
    sudo systemctl restart NetworkManager
    ```

12. For WPA2 networks (password required), add multiple known networks (if applicable):

    ```bash
    sudo nmcli device wifi connect "Network1" password "password1"
    sudo nmcli device wifi connect "Network2" password "password2"
    ```

13. Verify auto-connect behavior:

    ```bash
    nmcli connection show
    ```

14. Test swapping out Wi-Fi adapters:

    Make sure the SSH shell is connected to the host over wired Ethernet.

    ```bash
    # Swap out adapters and watch the connection happen...
    watch -n 0.2 nmcli device status
    ```
