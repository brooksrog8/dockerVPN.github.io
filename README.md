# dockerVPN.github.io

# Docker Proj

I first created a droplet on digital ocean and made a ssh key by using the following commands:
```shell
ssh-keygen
```
then ssh into your server and download docker

```shell
sudo apt-get remove docker docker-engine docker.io
sudo apt install docker.io
sudo snap install docker.io
```
If you get an error that says the kernel is outdated just update and reboot.

Then test to see if docker is working by running
```shell
sudo docker run hello-world
```

Now set up wireguard

run the following commands on your server 
```shell
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```

In your docker-compose file paste this

```shell
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Hong_Kong
      - SERVERURL=1.2.3.4
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```

TZ refers to the timezone so choose yours from the database https://en.wikipedia.org/wiki/List_of_tz_database_time_zones?ref=thematrix.dev
serverurl refers to the server IP address which you also should change.


Exit then start wireguard with the following commands.
```shell
cd ~/wireguard/
docker-compose up -d
```

then enter `docker-compose logs -f wireguard`
and scan the QR code from your phone in the wireguard app.


![IMG_7104](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/2d819030-3eb0-4644-b86c-0f4c69202d28)


![IMG_7105](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/eba79b32-cf4c-461a-af80-398bdea5b47a)



![IMG_7106](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/5aea2789-73e8-4f3c-8ac9-b3255945836e)

I had trouble getting my VPN to work with my desktop, its a bit different because there's no app for linux so you had to configure wg0



![Screenshot from 2023-12-11 02-38-39](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/73ecb2a5-7b7b-4709-96c2-ce106c55da1d)



I've looked at all the forums and nothing will work. Whatever I am missing must be very small. Here is my result when I try to ping. For some reason I get 100% packet loss.

![Screenshot from 2023-12-11 02-38-51](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/58b0a42c-fb33-4683-9db3-376abf2a444b)

And here is my result when I try wg

![Screenshot from 2023-12-11 02-44-42](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/4742b90d-bc5d-48f9-9601-b854ccfa621a)


 It says my connection is established by the keepalive packets so my connection is open.

 Here is my result when I try to ping Google

![Screenshot from 2023-12-11 02-47-43](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/810dd02d-a3e5-467d-9075-68773a57461e)





 And here's my wg0.conf for my server side

![Screenshot from 2023-12-11 02-59-45](https://github.com/brooksrog8/dockerVPN.github.io/assets/114731707/5efd45e1-15cc-44e2-badb-ba1a3693632f)

