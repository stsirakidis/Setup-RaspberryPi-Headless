# Setting up a Raspberry Pi headless with Ansible

## General

If you are going to use the Raspberry Pi OS image for the Raspberry Pi then you have first to copy the image on an SD card (or USB stick), boot your Pi, and you need finally a keyboard and a monitor to configure the following basic setup:

- hostname
- wireless networking
- enable SSH
- change the password of the pi user

Afterwards you can access your Pi wireless using an SSH connection (e.g. with Putty). There is a workaround that describes in the [Raspberry Documentation](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) how to configure the wireless networking and enable the SSH in advance.

Alternative you can use this Ansible Playbook **modify_raspios_image.yml** to modify the original Raspberry Pi OS image automatically. This will do the basic setup described above (set hostname, configure the wireless networking, enable SSH, and set a password for the pi user) by creating a modified Raspberry Pi OS image. At the end, you can copy this modified image on an SD card (or USB stick).

## How to use it

1. Create a new directory and clone this repository within it:

   ```bash
   git clone https://github.com/stsirakidis/Modify-RaspberryPiOS-Image.git
   cd Setup-RaspberryPi-Headless
   ```

2. Encrypt the **raspios_passwords.yml** file using Ansible Vault (remember the password):

   ```bash
   ansible-vault encrypt raspios_passwords.yml
   ```

3. Edit the **raspios_passwords.yml** file using also Ansible Vault:

   ```bash
   ansible-vault edit raspios_passwords.yml
   ```

   Set the following passwords in this file:

   - **pi_password**: The password for the pi user.
   - **wifi_psk**: The password of the Wi-Fi network that the RPi should connect to.

4. Edit the **raspios_vars.yml** file and set the values for the following parameters:

   ```bash
   vi raspios_vars.yml
   ```

   - **imagetype**: Set default image type in order not having to define it as a parameter, when you run the playbook with e.g. **-e 'imagetype="lite"'**. Value can be lite, desktop, or full.

   - **fullhash**: On the official [Download Raspberry Pi OS for Raspberry Pi](https://www.raspberrypi.org/downloads/raspberry-pi-os/) you can download the "*Raspberry Pi OS (32-bit) with desktop and recommended software*" image (the `full image`). There is a hash value (SHA-256) on the site for this image. Set this hash value for the `full image` in the **fullhash** variable. You don't need to change the **fullimage** variable, since this is already the link to download the latest Raspberry Pi OS full image.

   - **desktophash**: Equivalent to the **fullhash** variable, you have to set the hash value for the "*Raspberry Pi OS (32-bit) with desktop*" image (the `desktop image`). You don't need to change the **desktopimage** variable, since this is also the link to download the latest Raspberry Pi OS desktop image.

   - **litehash**: Equivalent to the **desktophash** variable, you have to set the hash value for the "*Raspberry Pi OS (32-bit) Lite*" image (the `lite image`). You don't need to change the **liteimage** variable, since this is the link to download the latest Raspberry Pi OS lite image, too.

   - **mount_dir**: The mount point that will be used temporarily from the Ansible Playbook to modify the Raspberry Pi OS image. You can leave the variable as it is.

   - **dest_image_path**: The directory that will be used temporarily from the Ansible Playbook to modify the Raspberry Pi OS image. You can leave the variable as it is.

   - **pi_hostname**: The hostname of the RPi.

   - **wifi_country**: Set the ISO code of your country (see Alpha-2 code in [Wikipedia](https://en.wikipedia.org/wiki/ISO_3166-1) and related infos also in the [Raspberry Documentation](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md).

   - **wifi_ssid**: The name of your Wi-Fi network. The Ansible Playbook will configure the wireless network so that it can also connect to this in case the name (SSID) is hidden.

## How to run it

Run the Ansible Playbook within the same working directory as follows.

- If you want to modify the lite image:

   ```bash
   ansible-playbook modify_raspios_image.yml -e 'imagetype="lite"' --ask-vault-pass
   ```

   If you have defined the variable as `imagetype: lite`, then you don't need to use the option `-e 'imagetype="lite"'` at the command line:

   ```bash
   ansible-playbook modify_raspios_image.yml --ask-vault-pass
   ```

- If you want to modify the desktop image:

   ```bash
   ansible-playbook modify_raspios_image.yml -e 'imagetype="desktop"' --ask-vault-pass
   ```

- If you want to modify the full image:

   ```bash
   ansible-playbook modify_raspios_image.yml -e 'imagetype="full"' --ask-vault-pass
   ```

In case that you get an error regarding the SUDO rights with your account (when you run the Ansible Playbook), then add the option `--ask-become-pass` to the command, e.g.:

- example:

   ```bash
   ansible-playbook modify_raspios_image.yml --ask-vault-pass --ask-become-pass

   ansible-playbook modify_raspios_image.yml -e 'imagetype="desktop"' --ask-vault-pass --ask-become-pass

   ansible-playbook modify_raspios_image.yml -e 'imagetype="full"' --ask-vault-pass --ask-become-pass
   ```

When you run the playbook, then it will first download the defined Raspberry Pi OS image to your working directory (e.g. `2020-05-27-raspios-buster-lite-armhf.zip`) and will place at the end of the playbook the modified image (e.g. `2020-05-27-raspios-buster-lite-armhf_modified.zip`) in the same directory.

The Ansible playbook was tested with CentOS 7 & Ubuntu 18.

Have fun!
