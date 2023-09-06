# RPI BREW

![RPI BREW LOGO](images/rpi_brew_logo.jpeg)

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
- Open the following ports in your router settings towards Raspberry Pi: 22, 80, 443
- Format the HDD to ext4 filesystem:  
<https://www.tecmint.com/create-new-ext4-file-system-partition-in-linux/>
- Setup the auto mount of the external HDD with fstab:  
<https://www.shellhacks.com/raspberry-pi-mount-usb-drive-automatically/>
- Install docker:  
<https://www.simplilearn.com/tutorials/docker-tutorial/raspberry-pi-docker>
- Install docker-compose:  
<https://dev.to/elalemanyo/how-to-install-docker-and-docker-compose-on-raspberry-pi-1mo>
- Configure your router to have the same internal IP for your Raspberry Pi everytime (Manual IP of device to DHCP list in router)
- Disable userland-proxy:  
  1. Create/edit ```/etc/docker/daemon.json```
  2. Add this:  

      ```yaml
      {
        "userland-proxy": false,
        "iptables": true
      }
      ```

  3. Restart docker: ```sudo systemctl restart docker```
  4. If not done automatically, it may also be necessary to run:  
  ```/sbin/sysctl net.ipv4.conf.docker0.route_localnet=1```
- Make sure to have DNS servers are permanently present in ```/etc/resolv.conf```:
  - ```sudo apt install resolvconf```
  - Open ```/etc/resolvconf/resolv.conf.d/base``` and add the following lines:  

    ```config
    nameserver 8.8.8.8
    nameserver 8.8.4.4
    ```

  - ```sudo resolvconf -u```
  - ```systemctl status systemd-resolved.service```
  - ```systemctl enable systemd-resolved.service```
  - Try ```resolvectl status```
- Free up port 53 for Pi-hole:
  - ```sudo systemctl stop systemd-resolved```
  - Edit ```/etc/systemd/resolved.conf```
    - ```DNSStubListener=no```
  - ```sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf```
- You might increase the default swap size from 100MB to 1GB:  
<https://nebl.io/neblio-university/enabling-increasing-raspberry-pi-swap>

---

## Preparation

- Clone the repository
- Create the following folders from the ```.env``` file:
  - ```HDD_PATH```
  - ```HDD_PATH/_backup```
  - ```HDD_PATH/_cache```
  - ```HDD_PATH_SHARED```
  - ```CONFIG_PATH```
  - ```JELLYFIN_HDD```
  - ```JELLYFIN_CACHE```
  - ```JELLYFIN_METADATA```
  - ```JELLYFIN_TRANSCODE_CACHE```
  - ```HDD_PATH_SHARED/downloads```
- Copy configurations (docker_appdata folder) from repository to ```CONFIG_PATH```

---

## Configure the environment

- Setup Dynamic DNS: Register and login to <https://www.dynu.com/en-US/ControlPanel/DDNS> or <https://www.duckdns.org/>, and add a subdomain you wish
- ```sudo chmod 600 docker_appdata/letsencrypt/acme.json```
- Update ```.env``` file based on the comments in the file
- Update ```${CONFIG_PATH}/authelia/configuration.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/authelia/users_database.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/prometheus/prometheus.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/grafana/provisioning/datasources/datasource.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/grafana/config.monitoring``` based on the comments in the file
- Update ```${CONFIG_PATH}/homepage/bookmarks.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/homepage/services.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/homepage/settings.yml``` based on the comments in the file
- Update ```${CONFIG_PATH}/homepage/widgets.yml``` based on the comments in the file
- ```sudo docker compose --profile all up -d --force-recreate --remove-orphans```
- You can reach each container with the address defined in the ```.env``` file

---

## Configure the containers

- Grafana:
  - Set allowed cookies of Authelia session cookie:
    - ```Home --> Connections --> Data sources --> Prometheus --> Allowed cookies: Add "authelia_session" --> Click Save & test```
- Jellyfin:  
(These steps are must, if you want to save space during Jellyfin streaming with transcoding, cache and metadata,since it is generating huge files, so I suggest to put it into the HDD, not to SD card where the default config sets)
  - Set Jellyfin custom transcode cache path to your prevously set in the ```.env```: ```Settings --> Playback --> Transcode path``` (dont forget to click on save after)
  - Set Jellyfin custom cache path to your prevously set in the ```.env```: ```Settings --> General --> Cache path``` (dont forget to click on save after)
  - Set Jellyfin custom metadata path to your prevously set in the ```.env```: ```Settings --> General --> Metadata path``` (dont forget to click on save after)
  - Add your Rasbpberry Pi's IP address as a primary DNS server to your router configuration to make sure that every of your device conntcted to the router will be ad-free
    - Optional: update Pi-hole ad-list with additional database URLs

---

## Containers used

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
- <https://hub.docker.com/r/prom/prometheus>
- <https://hub.docker.com/r/prom/node-exporter>
- <https://hub.docker.com/r/grafana/grafana>
- <https://github.com/benphelps/homepage/>
- <https://hub.docker.com/r/linuxserver/duplicati>
- <https://hub.docker.com/r/authelia/authelia>
- <https://uptime.kuma.pet/>

---

## Useful links

- <https://www.dynu.com/en-US/ControlPanel/DDNS>
- <https://www.duckdns.org/>
- <https://github.com/vegasbrianc/docker-traefik-prometheus/tree/master>
- <https://gethomepage.dev/>
- <https://github.com/marcogreiveldinger/videos/tree/main/authelia>
- <https://technotim.live/posts/authelia-traefik/>
