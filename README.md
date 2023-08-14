# RPI BREW

Docker containers for Raspberry Pi based Advanced Home Server

---
## Prerequisites

- Raspberry Pi 2-4
- Class10 Micro SD Card with minimum 16GB, but suggested 32GB storage capacity
- External HDD
- LAN cable (Wifi could be ok, but if you plan with higher traffic, I suggest LAN cable)
- Optional passive cooling case

---

## Installation

- Install Raspberry Pi OS based on this instruction  
(I used the 64bit lite image for my Raspberry Pi 4 4GB version):  
<https://www.raspberrypi.com/documentation/computers/getting-started.html#using-raspberry-pi-imager>
- Format the HDD to ext4 filesystem:  
<https://www.tecmint.com/create-new-ext4-file-system-partition-in-linux/>
- Setup the auto mount of the external HDD with fstab:  
<https://www.shellhacks.com/raspberry-pi-mount-usb-drive-automatically/>
- Install docker:  
<https://www.simplilearn.com/tutorials/docker-tutorial/raspberry-pi-docker>
- Install docker-compose:  
<https://dev.to/elalemanyo/how-to-install-docker-and-docker-compose-on-raspberry-pi-1mo>

---

## Preparation

- Clone repository
- Create the following folders from the .env file:
  - HDD_PATH_SHARED
  - CONFIG_PATH
  - JELLYFIN_TRANCODE_CACHE
  - ${HDD_PATH_SHARED}/downloads
- Copy configurations (docker_appdata folder) from repository to CONFIG_PATH

---

## Configuration

- Setup Duckdns: Register and login to <https://www.duckdns.org/>, and add a subdomain you wish
- Set Jellyfin custom transcode cache path: <https://github.com/jellyfin/jellyfin/issues/2189#issuecomment-568740643>  
(This step is a must, if you want to save space during Jellyfin streaming with transcoding, since it is generating huge files, so I suggest to put it into the HDD)
- sudo chmod 600 docker_appdata/letsencrypt/acme.json
- Update .env file based on the comments in the file
- update ${CONFIG_PATH}/traefik/rules.toml based on the comments in the file
- sudo docker-compose up -d --force-recreate
- You can reach each container with the address defined in the .env file

---

## Containers

- Container's original descriptions:
  - <https://hub.docker.com/r/containous/whoami>
  - <https://hub.docker.com/r/dperson/samba>
  - <https://hub.docker.com/r/vladgh/minidlna>
  - <https://hub.docker.com/r/linuxserver/wikijs>
  - <https://hub.docker.com/r/linuxserver/librespeed>
  - <https://hub.docker.com/r/hurlenko/filebrowser>
  - <https://hub.docker.com/r/linuxserver/jellyfin>
  - <https://hub.docker.com/r/linuxserver/dokuwiki>
  - <https://hub.docker.com/r/linuxserver/duckdns>
  - <https://hub.docker.com/r/containrrr/watchtower>
  - <https://hub.docker.com/r/linuxserver/qbittorrent>
  - <https://hub.docker.com/r/pihole/pihole>
  - <https://hub.docker.com/r/linuxserver/heimdall>
  - <https://hub.docker.com/_/traefik>
  - <https://hub.docker.com/r/portainer/portainer-ce>
  - <https://hub.docker.com/r/selfhostedpro/yacht>
