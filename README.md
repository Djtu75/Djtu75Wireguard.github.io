# Djtu75Wireguard.github.io

## Step 1: Obtain a Cloud VM

-   Go to <https://www.digitalocean.com/>

-   Create an account or login if you already have one

-   Go to the left side of the screen and select first project. (this should be the default project, create a new one if needed)


<img width="1918" height="1018" alt="Screenshot 2025-11-23 220644" src="https://github.com/user-attachments/assets/9f1e3dc7-9d88-4a5c-adef-7bb273c220a5" />



-   Press the add a payment method button

-   Add your billing method

-   Double check that in the top right by your profile it says "free trial active" like below


<img width="600" height="394" alt="image" src="https://github.com/user-attachments/assets/f7e06307-dfe6-4218-9596-e8206b1b540d" />



-   Press droplets on the left side of the screen and press create a droplet


<img width="1917" height="1018" alt="Screenshot 2025-11-23 221321" src="https://github.com/user-attachments/assets/8fe066ce-c4d4-4c50-aeb7-6523e86a7517" />



-   Choose an appropriate region

-   Choose ubuntu as your image

-   Choose a basic droplet type

-   CPU options: regular and $6/month


<img width="1611" height="697" alt="Screenshot 2025-11-23 222539" src="https://github.com/user-attachments/assets/4a7d935b-89dc-4c38-9787-6f6842a49549" />



-   Choose the password authentication method and choose a password

-   Press create droplet

-   Go back to your first project and press the 3 dots next to your droplet for settings

-   Choose access console

-   Click Launch Droplet Console


<img width="1918" height="907" alt="Screenshot 2025-11-23 232852" src="https://github.com/user-attachments/assets/fa2d02b5-b1f2-487e-bfb2-2ea1946ea15d" />


------------------------------------------------------------------------

## Step 2: Set Up a Container Within Your Droplet

-   In the console begin by installing Docker

```
sudo apt update

sudo apt install -y docker.io docker-compose

sudo systemctl enable Docker
```

-   Enable ip forwarding by editing the following config file like so:

```
sudo vim /etc/sysctl.conf
```

-   And uncomment the line

```
net.ipv4.ip_forward=1
```

-   Then run to load the change

```
sudo sysctl -p
```

-   We will use the Docker image from this link
<https://hub.docker.com/r/linuxserver/wireguard>

-   Which also helpfully provides the instructions on how to create the container with the correct variables.

-   Converting their docker run command into a docker compose up, make this docker-compose.yaml file:

```
services:

    wireguard:

        image: lscr.io/linuxserver/wireguard:latest

        cap_add:

            \- NET_ADMIN

            \- SYS_MODULE

        environment:

            \- PUID=1000

            \- PGID=1000

            \- TZ=Etc/UTC

            \- SERVERURL=auto

            \- SERVERPORT=51820

            \- PEERS=2

            \- PEERDNS=1.1.1.1

            \- INTERNAL_SUBNET=10.13.13.0

        volumes:

            \- ./config:/config

            \- /lib/modules:/lib/modules:ro

        ports:

            \- 51820:51820/udp

        sysctls:

            \- net.ipv4.conf.all.src_valid_mark=1

        restart: unless-stopped
```

-   Then run

```
systemctl enable docker

docker-compose up --build -d
```

------------------------------------------------------------------------

## Step 3: Connect to the Running Wireguard

-   You should now have a running wireguard container

-   Check this by running

```
docker container ls
```

-   Then use the name of the container (mine was wireguard_wireguard_1) to get the logs

```
docker logs -f wireguard_wireguard_1
```

-   The logs will contain 2 QR codes for the two peers you set when making the container

-   Scan both with a phone, and send the config files to yourself via email/drive/etc.

-   To connect with a windows pc, go to <https://www.wireguard.com/install/>, download the installer, and run it

-   From there you can use the gui to import it from file (it's also doable by terminal, but mine kept giving me permission errors, thanks windows)

-   You can then test it by going to an IP check website

-   Here you can see my IP with wireguard disabled


<img width="1918" height="1018" alt="Screenshot 2025-11-24 021634" src="https://github.com/user-attachments/assets/5d92a607-d5ab-4e47-b1b4-dc9f604ffa59" />



-   And then with wireguard enabled


<img width="1917" height="1022" alt="Screenshot 2025-11-24 021612" src="https://github.com/user-attachments/assets/5df1062e-b2a0-4016-8708-2570bb5e8112" />



And that's all!
