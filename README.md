# Raspberry Pi 4 Model B 4GB RAM

After getting Raspberry Pi OS (Legacy, 64-bit) Lite image (with ssh enabled and a user/password created) on to micro SD card through [Raspberry Pi Imager](https://www.raspberrypi.com/software/):

1) inserted the micro SD card into Raspberry Pi, connected it to my ISP-provided router with an ethernet cable, and powered it on. After waiting for a minute or so, the Pi showed up on the admin console of the router. It was given the IP address, 192.168.1.64

2) logged into Pi from a terminal on my Mac:

    ```ssh rpi44gb@192.168.1.64```

3) updated packages. (the following update / upgrade / autoremove can be run later also when the system needs to be updated with the latest Docker packages and/or other packages)

    ```
    sudo apt update && sudo apt upgrade -y && sudo apt autoremove
    ```

4) set the IP address (from step 1) above) as static IP address on Pi:
    ```
    sudo systemctl enable NetworkManager
    sudo systemctl start NetworkManager
    nmcli device show eth0
    sudo nmtui edit “Wired connection 1” # Pi / Gateway / Router IP Address are set here, renamed "Wired connection 1" to "Wired_connection_1" (spaces in the name were an issue)
    sudo shutdown -r now
    ```
5) logged back into Pi from a terminal on my Mac:

    ```
    ssh rpi44gb@192.168.1.64
    mkdir -p ~/adguardhome/conf ~/adguardhome/work
    ```

6) Installed Docker and Portainer (I'll be using Portainer web UI to manage Docker containers):
    ```
    curl -sSL https://get.docker.com | sh
    docker version
    sudo systemctl enable docker
    sudo usermod -aG docker rpi44gb
    sudo docker pull portainer/portainer-ce:linux-arm64
    sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:linux-arm64
    ```
7) Accessed Portainer admin page via web browser (at 192.168.1.64:9000) for setup.

8) With [docker-compose.yaml](docker-compose.yaml) loaded as stack in Portainer and environment variables from [rpi44gb.env](rpi44gb.env), I now have Docker / Portainer / AdGuard Home running on my Pi

9) Accesessed AdGuard Home initial admin page at (at 192.168.1.64:3000) for setup. After initial setup, AdGuard Home can later be administered from 192.168.1.64:3001

10) Portainer can be upgraded later when there is a newer version:
    ```
    sudo docker stop portainer && sudo docker rm portainer && sudo docker pull portainer/portainer-ce:linux-arm64 && sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:linux-arm64
    ```
