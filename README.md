# Cloud-Docker-VPN-Project
# Pre-requisites
### Step 1. Create a Digital Ocean Account
- I used my @hawaii.edu account

### Step 2. Create a new droplet and name it ubuntu-docker-wireguard. Make the selections below when creating the droplet:
- Region - New York
- Datecenter - NYC1
- Image - Ubuntu
	- Version - 22.04 (LTS) x64
- Size - Basic at $4/month
- SSH or Password (I used password)

# Install Docker and Docker-Compose on Ubuntu 22.04
#### Reference Link: https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/

### Step 1. Install necessary tools
`sudo apt install apt-transport-https ca-certificates curl software-properties-common -y`
### Step 2. Add Docker key:
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
### Step 3. Add Docker repo (choose the correct repository, I am on mac I will choose the ARM 64bit)
`sudo add-apt-repository \ "deb [arch=arm64] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) \ stable"`
### Step 4. Switch to correct repo:
`apt-cache policy docker-ce`
### Step 5. Install Docker
`sudo apt install docker-ce -y`
### Step 6. Install Docker-Compose
`sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
- Set permission:<br>
`sudo chmod +x /usr/local/bin/docker-compose`

# Setup Wireguard
#### Reference Link: https://thematrix.dev/setup-wireguard-vpn-server-with-docker/

### Step 1. Create a "wireguard" directory with a subdirectory "config" and open the nano text editor to edit a file named "docker-compose.yml"
`mkdir -p ~/wireguard/`<br>
`mkdir -p ~/wireguard/config/`<br>
`nano ~/wireguard/docker-compose.yml`<br>
### Step 2. Copy and paste the content below in the "docker-compose.yml"
```
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
#### Step 2a. Modify a few lines in "docker-compose.yml"
1.  `TZ` refers to timezone. Choose yours from `TZ database name` from [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).
2.  `SERVERURL` refers to the server IP address. Find it on Vultr or DigitalOcean dashboard.
3.  `PEERS` are the number of user-config-files to generate, or the names of user-config-files. If you enter `PEERS=3`, it will generate `peer_1`, `peer_2` and `peer_3`. If you enter `PEERS=pc1,pc2,phone1`, it will generate `peer_pc1`, `peer_pc2` and `peer_phone1`.<br>
Example: ![image](https://user-images.githubusercontent.com/46617761/235452639-b939d755-5384-47ff-bb45-253d13317271.png)
- Hit `CTRL` + `X`, `Y`, `ENTER` to save and exit the file
### Step 3. Start Wireguard
`cd ~/wireguard/`<br>
`docker-compose up -d`<br>
### Step 4. Connect phone or desktop/mac to Wireguard (I have a mac laptop)
#### Step 4a. Copy file from server to local machine
  `scp username_here@IP_address:~/wireguard/config/peer_brittpc1/peer_brittpc1.conf ~/Downloads/`
#### Step 4b. Before VPN is on, open two browsers and go to ipleak.net on both. Show local IP info for both
#### Step 4c. Open .conf file in Wireguard application and activate
#### Step 4d. Refresh ONLY one browser to update the new IP address of the VPN
### Step 5. Take screenshot of Confirmation
![image](https://user-images.githubusercontent.com/46617761/235452763-0e88f1e1-a346-49bd-9f17-385a2980a925.png)
