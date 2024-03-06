# Kali installation on Raspberry Pi

## Download the ARM Image

Go to https://www.kali.org/get-kali/#kali-arm and download the latest image

or you can do

`wget https://kali.download/arm-images/kali-2024.1/kali-linux-2024.1-raspberry-pi5-arm64.img.xz`

## write image to disk using dd

⚠️ **ALERT :** Do not forget to change /dev/\<yourMicroSDCard> ⚠️

`xzcat kali-2024.1/kali-linux-2024.1-raspberry-pi5-arm64.img.xz | sudo dd of=/dev/<yourMicroSDCard> bs=4M status=progress`

## Connect to your Raspberry Pi

You can connect to your Rpi using ssh with the default creds : kali/kali

`ssh kali@<kalisIP>`

## Ansible setup

To make life easier, you can use init.sh.sample script to create 2 accounts. One for the main user (aka fr3sh in the script) and one for ansible (aka shiva in the script)

You have to change the passwords for fr3sh and ansible.
You can do that using this command line : 
`mkpasswd --method=SHA-512 --stdin`

You also have to generate your own keys using `keygen` for both users and paste the public keys in the init.sh.sample script. I did protect both keys with a password although you might want not to use a password for ansible for convinience.

rename `init.sh.sample` as`init.sh`
run `init.sh` as root. Go get some tea... 🫖

## Running ansible

⚠️ **ALERT :** Change the IPs of your devices in the inventory file ⚠️

Change the users in roles > bootstrap > vars > main.yml
The users have to be the same as in the init.sh file

modify the files in roles > KaliPi > files

* kalipi.ovpn.sample will help connecting to your openvpn server. You'll have to set up one and paste the client config in here. Don't forget to rename it kalipi.ovpn

* rpi_id_rsa.sample is needed for autossh in order to connect to your local machine.

* autossh will try to connect to your local machine creating a tunnel allowing you to ssh to the KaliPi5
Using the tunnel : 
`ssh -i ~/.ssh/kalipi_id_rsa fr3sh@localhost -p 2222`

Then run ansible with the following command :

`ansible-playbook -u ansible -K bootstrap.yml --ssh-extra-args "-o IdentitiesOnly=yes"`

That will run both playbooks :

* bootstrap

* KaliPi