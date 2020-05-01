# Arcadas Server Environment

## OS installation

Ubuntu 18.04.2 LTS Server ([download](https://ubuntu.com/download/server)) \
Create USB installer on unix like sytems in CLI.

```sh
# List disks (search the USB drive name - sdX)
sudo run fdisk -l
# Change ISO path and X (without number)
sudo dd if=/path/to/downloaded/iso of=/dev/sdX
```

Boot from USB on the server. Istall Ubuntu Server with *OpenSSH, Docker* and *AWS*.

After first boot:

```sh
sudo apt-get update
```

## Must have apps

```sh
sudo apt-get -y install vim mc ufw git xclip nmap pm-utils
```

## Install and config SSH

SSH key generation: [keygen](https://www.ssh.com/ssh/keygen/) \
Clipboard copy: [pbcopy/xclip](https://programmer.help/blogs/terminal-copy-and-paste-xclip-clip-xsel-and-pbcopy-linux-mac-and-ubuntu.html)

```sh
# Client - Copy public key
cat ~/.ssh/id_rsa.pub | pbcopy
```

```sh
# Server - Paste public key
vim ~/.ssh/authorized_keys
```

```sh
# Server - Check permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

```sh
# Client - Check connection by key
ssh <server_username>@<server_ip_address>
```

```sh
# Server - Disabling SSH Password Authentication
sudo vim /etc/ssh/sshd_config
# Search for the following
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM no
```

Generate SSH key on the server

```sh
# Server - Generate key
ssh-keygen -t rsa -b 4096 -C "<email@address.com>"
```

## Setup Github

SSH key:

```sh
# Client (Not on the server!) - Copy public key to clipboard (Change the IP!)
ssh 192.168.0.21 "cat ~/.ssh/id_rsa.pub" | pbcopy
```

Paste it into Github - Settings. Now you can pull this repo:

```sh
mkdir ~/github
cd ~/github
git clone git@github.com:arcadas/arcadas-server.git
# Are you sure you want to continue connecting (yes/no)? yes
```

Ignore file mode (edit code over samba share):

```sh
# Change directory to $HOME
cd
# Edit global git config
vim .gitconfig
# Ignore file mode
[core]
   fileMode = false
```

## Bash aliases

Create backup and copy all files from bash to home folder and run:

```sh
cd
cp .bashrc .bashrc_original
cp -a github/arcadas-server/bash/. .
source .bashrc
```

Check all aliases before use them, please! \
You can disable all types of aliases in .bashrc.

## Automount HDDs

Multiple additional services use these shared devices.

__Check lables and FS type before run!__

```sh
# Alias - List all available disks
lsd
# Alias - Edit fstab
fse
# Add disks by lable with name
/dev/sda1 /media/nas ext4 defaults 0 0
/dev/sdc2 /media/arcadas ext4 defaults 0 0
```

Reboot

```sh
reboot
```

Set group for volumes

```sh
sudo chown -R arcadas:arcadas /media/nas
sudo chown -R arcadas:arcadas /media/arcadas
sudo chmod 777 -R /media/nas/torrent
```

## Samba shares

Install and config.

```sh
sudo apt-get -y install samba
sudo smbpasswd -a arcadas
sudo cp /etc/samba/smb.conf ~/.config_original
sudo vim /etc/samba/smb.conf
# Add this to the very end of the file:
[nas]
   path = /media/nas
   valid users = arcadas
   guest ok = no
   read only = no

[arcadas]
   path = /media/arcadas
   valid users = arcadas
   guest ok = no
   read only = no

[github]
   path = /home/arcadas/github
   valid users = arcadas
   guest ok = no
   read only = no
# Restart
sudo service smbd restart
```

## Setup Hosts File

Every service will be reachable on a subdomain of arcadas.com by Traefik reverse-proxy service.

We have to define these subdomains in our hosts file:

```sh
192.168.0.21 arcadas.com
192.168.0.21 portainer.arcadas.com
```

## Docker

Install Docker: [docker-ce/ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) \
Install Docker Compose: [Compose](https://docs.docker.com/compose/install/)

```sh
# Build and run:
cd ~/path/to/docker-compose.yml
docker-compose up -d
# Up for only certain container:
docker-compose up -d <service>
# Stop:
docker container stop transmission
# Shell access whilst the container is running:
docker container exec -it transmission /bin/bash
# To monitor the logs of the container in realtime:
docker container logs -f transmission
# Restart service
sudo systemctl restart docker.socket docker.service
```

Further commands and aliases: dotfiles/.bash_aliases_ops

## Traefik Reverse Proxy

Free port 80 (e.g.: from NginX)

```sh
sudo systemctl disable nginx
sudo service nginx stop
```

Run Traefik Service

Documentation: https://docs.traefik.io/

```sh
# Create a custom network
docker network create proxy
# Start Traefik from local compose file
cd traefik
docker-compose up -d
```

For Traefik to discover our services, we have to add labels to our services and put services to the same network (proxy). In docker-compose.yml definition we have to add the following definitions:

```sh
version: '2'

services:

  service:
    image: <image>
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.cal.rule=Host(`<service>.arcadas.com`)"
      - "traefik.docker.network=proxy"
      - "traefik.http.services.cal.loadbalancer.server.port=<port>"

networks:
  proxy:
    external: true
```

## Sudoers

Run `sudo` command without entering a password.

```sh
# Find the paths to command files
which pm-suspend docker
# Edit sudoers file
sudo visudo
# Add user with no passwords commands
arcadas ALL=(ALL) NOPASSWD: /usr/sbin/pm-suspend
# In nano editor: CTRL+O, ENTER, CTRL+X (save and exit)
```

## Certificates for Development

Setup SSL Certificate by OpenSSL.

Documentation: [Certificates for localhost](https://letsencrypt.org/docs/certificates-for-localhost/)

```sh
# Generate certificate
# !!! Change localhost to domain name (e.g.: transmission.arcadas.com)
openssl req -x509 -out localhost.crt -keyout localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

```sh
# Move into .ssh folder
# Change loclahost to domain name
mv localhost.* ~/.ssh/
```

Import certificate into your client OS, for example mac:

Change localhost name to domain name. \
`Keychain Access -> File -> Import Items... -> localhost.crt`

Open certificate and select `Always Trust`.

## Troubleshooting

### Wrong timezone issue

```sh
# Show actual timezone
timedatectl status
# Check our timezone in the list
timedatectl list-timezones | grep Budapest
Europe/Budapest
# Set timezone
sudo timedatectl set-timezone Europe/Budapest
```

### SSH Authentication Refused

If you unable to authenticate over SSH. Check this log:

```sh
sudo tail -f /var/log/secure
Sep 14 01:26:31 new-server sshd[22107]: Authentication refused: bad ownership or modes for directory /home/dave/.ssh
```

Solution:

```sh
# Fix user directories rights
chmod g-w /home/your_user
chmod 700 /home/your_user/.ssh
chmod 600 /home/your_user/.ssh/authorized_keys
```

### Docker Container Stop/Restart issue

```sh
docker restart 5ba0a86f36ea
Error response from daemon: Cannot restart container 5ba0a86f36ea: [2] Container does not exist: container destroyed
Error: failed to restart containers: [5ba0a86f36ea]
```

Solution:

```sh
# Reboot the host
reboot
```

StackOverflow: [cannot-stop-or-restart-a-docker-container](https://stackoverflow.com/questions/31365827/cannot-stop-or-restart-a-docker-container)

### Transmission Permission Denied

Error: `Error: Unable to save resume file: Permission denied` \
Possible another error is that the owner of transmission is `911`.

Solution:

```sh
# Set the proper owner
sudo chown -R arcadas:arcadas ~/.config/transmission
# Set the proer rights
sudo chmod -R g+rw ~/.config/transmission
```

StackOverflow: [transmission-started-with-a-permission-denied-now-it-wont-even-run](https://askubuntu.com/questions/522307/transmission-started-with-a-permission-denied-now-it-wont-even-run)

### Docker-compose fails on macOS

Error

```sh
docker.errors.DockerException: Credentials store error: StoreError('Credentials store docker-credential-desktop exited with "No stored credential for https://index.docker.io/v1/".')
```

Solution

Delete `credsStore` value in `~/.docker/config.json` and restart Docker:

```json
{
   "auths": {},
   "credsStore": "",
   "experimental": "disabled"
}
```