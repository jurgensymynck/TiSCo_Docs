# Tisco Host User Manual

- [Connections](#connections)
- [Startup](#startup)
- [Log in a 'Remote Desktop' session](#log-in-a-remote-desktop-session)
- [docker compose from the command line](#docker-compose-from-the-command-line)
- [Checking for the correct driver for the graphics card](#checking-for-the-correct-driver-for-the-graphics-card)
- [Cloning an existing TiSCO host to a new one](#cloning-an-existing-tisco-host-to-a-new-one)
- [Checklist](#checklist)


## Connections

1. Power cable.
1. One ASUS Wi-Fi dongle, That has a white label with black text marking.
1. Connect the disco Arduino Board using a small USB cable to one of the4 back usb connectors (Does not matter which).
1. Check or connect that the push button Usb connector is also connected to the host via the front or the back USB ports (Does not matter which).
1. no keyboard , mouse or display Need to be connected.

## Startup

1. boot  the host and allow  two minutes Of boot time.
1. On the windows guest, run the `tisco_find_host.py` Script that will attempt tofind up and running disco hosts that are in the same subnet.
1. On success, The script provides the ip addresses of one or more hosts, together with the appropriate ports to make connections.

## Log in a 'Remote Desktop' session

1. On the Windows client, start 'Connect to external desktop' or something like that.
1. Connect to the font ip with the appropriate port.

    warning: You must provide user: `tisco` and the accompanying password. Do not use other credentials! You have to go to 'login using other credentials' or similar.

        # example

        host: 192.168.0.241:3389
        user: tisco
        password: ****************

## docker compose from the command line

1. open up `ssh` terminal to the host
1. Switch to the `Cosmos-project` folder

## Checking for the correct driver for the graphics card

`Xeon E3-1200 v2/3rd Gen` Should use the built in `i915` driver

        sudo lshw -c video

        *-display
        description: VGA compatible controller
        product: **Xeon E3-1200 v2/3rd Gen** Core processor Graphics Controller
        vendor: Intel Corporation
        physical id: 2
        bus info: pci@0000:00:02.0
        version: 09
        width: 64 bits
        clock: 33MHz
        capabilities: msi pm vga_controller bus_master cap_list rom
        configuration: **driver=i915** latency=0
        resources: irq:34 memory:f7800000-f7bfqffff memory:e0000000-efffffff ioport:f000(size=64) memory:c0000-dffff

## Cloning an existing TiSCO host to a new one

UNTESTED

When cloning a Linux system to another host, it's essential to modify certain system configurations to avoid conflicts, especially in network environments. Here’s a checklist

1. Update the Hostname Properly.

        #Replace with the new hostname.
        nano /etc/hostname

2. Update Hosts File:

        nano /etc/hosts

        # Replace old hostname references with the new one
        127.0.1.1   new-hostname

3. Regenerate SSH Host Keys (Important for Security)
To prevent SSH from detecting the clone as the same host. This generates new SSH key fingerprints:

        sudo rm /etc/ssh/ssh_host_*
        sudo dpkg-reconfigure openssh-server
        sudo systemctl restart ssh

4. Change the Machine-ID** The `machine-id` is a unique identifier for systemd-based systems.
5.
        # Clear and regenerate
        sudo truncate -s 0 /etc/machine-id
        sudo systemd-machine-id-setup

5. Update Network Configuration

TODO - UNFINISHED

- Clear persistent network rules:

        sudo rm /etc/udev/rules.d/70-persistent-net.rules

6. Review Unique Identifiers in Services**

- **Cron jobs:**  Check `/etc/crontab` and `crontab -e` for host-specific tasks.

- **NFS/SMB clients:**  Update `/etc/fstab` if mounting shares based on hostname.

- **Docker:**  If Docker is used, reset IDs:

        sudo rm /var/lib/docker/machine-id
        sudo systemctl restart docker

7. Clear Log Files (Optional)
To avoid confusion when troubleshooting:

        sudo find /var/log -type f -exec truncate -s 0 {} \;

8. Verification Steps
   - Confirm hostname:

            hostnamectl

   - Verify unique `machine-id`:

            cat /etc/machine-id

   - Check SSH keys:

            ssh-keygen -lf /etc/ssh/ssh_host_rsa_key

## Checklist

**TODO WIP**

- check devices `/dev/ttyACM0`
- check privileges of `/dev/ttyACM0`
- check group access of `tisco`
- check serial output with `cat` or `screen`
- ...