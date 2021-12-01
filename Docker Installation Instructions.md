# Definitions & General Information

Docker - Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. More information at:

https://www.docker.com/

IPFS - IPFS is The InterPlanetary File System is a protocol and peer-to-peer network for storing and sharing data in a distributed file system. More information at:

https://ipfs.io/

Seed - Seed is an identifier of the Stellar wallet. You must create it before you are starting to deal with a dockers

Nickname- Nickname is some user friendly name that is used in Tor. 

# Prerequisites

## Install docker:

    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    chmod +x /usr/bin/docker
    sudo groupadd docker
    sudo usermod -aG docker $USER && newgrp docker

## Docker minimal requirements:

    Memory: 512MB RAM (2GB Recommended). Disk: Sufficient amount to run the Docker containers you wish to use. CPU: 2 cores

## Create workspace directory:

    torplusworkspace=<yourworkspacedir>
    mkdir -p ${torplusworkspace}
    cd ${torplusworkspace}

## Login to docker registry:

Use login and password is secret

    echo 'ide!$QjNSF@e$8xX' | docker login --username torplusdev --password-stdin

# Run IPFS client

## Pull image
    docker pull torplusdev/production:ipfs-latest

## Run Tor-Plus container with IPFS:

## create workspace

    cd ${torplusworkspace}
    nickname=tunick21 # set your nickname
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ # set your seed
 
## run docker container
    docker run \
    --name torplusipfs \
    -p 28000:28080 \
    -e nickname=${nickname} \
    -e PP_ENV=prod \
    -e seed=${seed} \
    -v ${PWD}/tor:/root/tor \
    -v ${PWD}/ipfs:/root/.ipfs \
    -v ${PWD}/hidden_service:/root/hidden_service \
    --rm \
    torplusdev/production:ipfs-latest

## Add new files to ipfs

#Commands are executed in a new terminal with a running docker container on the computer

    torplusworkspace=<yourworkspacedir>
    cd ${torplusworkspace}/ipfs
    sudo mkdir -p ./data
    sudo cp ~/<the path to the file that we will upload to the ipfs network> /${torplusworkspace}/ipfs/data  # The file that we will upload to the ipfs is copied to the folder
    sudo docker exec -it torplusipfs /bin/bash
     ./ipfs add ~/.ipfs/data/<file name>  # After successfully uploading the file to the ipfs, a message is displayed "added <cid> <file name>"
     ./ipfs get "cid"

## Check uploaded files ipfs:

#Copy cid link from previous step. 

    Open Chrome browser with TorPlus installed on your computer and follow the link http://localhost:8080/ipfs/"cid"  
    # Playback or display of the file uploaded to the ipfs will start, if the file format is not supported by the site, 
    then it will be downloaded to the computer. Attention!!! This verification will be charged for using the TorPlus network!
    
## Setting and testing Video site:

    1. Go to the site https://torplus.videotpdemo.com/wp-admin/
    2. Enter login "admin" and password "pP6LPt9IQ9e@0BkJIA" and click on the login button.
    3. At the top left, move the cursor over the drop-down menu "da-video" and click on "Visit Site".
![alt tag](https://user-images.githubusercontent.com/52072466/143416916-0071782d-16df-46c8-abc4-4b5501e0f74c.png)

    4. Go to the video page (for example https://torplus.videotpdemo.com/video/sefozie-world/).
    5. Click on the "Edit Video" button.
![image](https://user-images.githubusercontent.com/52072466/143417235-44c2b2b4-f9a3-4d22-a33c-d1e99b11f8db.png)

    6. In the "Video data" block, change the path to the video in the field "Embed Video". (for example <iframe src="https://torplus.videotpdemo.com/ipfs/<your CID here>" allowfullscreen></iframe>). Сlick on the update button.
![image](https://user-images.githubusercontent.com/52072466/143417536-fcb44ffc-849b-4a2b-bf85-aa5ebfccc357.png)

    7. Go to the video page https://torplus.videotpdemo.com/video/sefozie-world/ and the added video will be displayed on the page.


# Run web site as host

## Create folder for ssl and copy ssl to dir

    torplusworkspace=<yourworkspacedir>
    mkdir -p ${torplusworkspace}
    cd ${torplusworkspace}
    mkdir -p ssl

## If use let's encrypt:

    #install certbot:
    sudo apt update && sudo apt install -y certbot

    domain=<yourdomains>
    email=<youremail>
    certbot certonly --standalone -d ${domain} \
                --non-interactive --agree-tos --email ${email} \
                --http-01-port=80

    cat /etc/letsencrypt/live/${domain}/fullchain.pem /etc/letsencrypt/live/${domain}/privkey.pem > ${torplusworkspace}/ssl/${domain}.pem

## Pull docker image:

    docker pull torplusdev/production:ipfs_haproxy-latest

## For host static files

    #Set static files:
        cd ${torplusworkspace}
        mkdir static 
        echo "Hello" >> ./static/index.html # or copy your static files

## Run docker image:

    cd ${torplusworkspace}
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ  # set your seed
    nickname=tum332 # set your nickname
    
## Run docker
    docker run \
        --name torplus \
        -e nickname=${nickname} \
        -e seed=${seed} \
        -e role=hs_client \
        -e HOST_PORT=80 \
        -e PP_ENV=prod \
        -e http_address=127.0.0.1 \
        -e useNginx=1 \
        -p 80:80 \
        -p 28000:28080 \
        -v ${PWD}/tor:/root/tor \
        -v ${PWD}/ipfs:/root/.ipfs \
        -v ${PWD}/ssl:/etc/ssl/torplus/ \
        -v ${PWD}/hidden_service:/root/hidden_service \
        -v ${PWD}/static:/var/www/html \
        --rm \
        torplusdev/production:ipfs_haproxy-latest

## Add text record to DNS:

    cat ${torplusworkspace}/hidden_service/hsv3/hostname
 
    torplus=<onion address without .onion suffix>

#For check TXT record you can use 
https://www.whatsmydns.net/#TXT/torplus.{domain}

## Add torplus subdomain to your domain

    In the domain manager you need to add forward subdomain of torplus to https://torplus.com

# Host from another ip or host or localhost site

## Create folder for ssl and copy ssl to dir

    torplusworkspace=<yourworkspacedir>
    mkdir -p ${torplusworkspace}
    cd ${torplusworkspace}
    mkdir -p ssl

## If use let's encrypt:

## install certbot:
    apt update && apt install -y certbot

    domain=<yourdomains>
    email=<youremail>
    certbot certonly --standalone -d ${domain} \
                --non-interactive --agree-tos --email ${email} \
                --http-01-port=80
    
    cat /etc/letsencrypt/live/${domain}/fullchain.pem /etc/letsencrypt/live/${domain}/privkey.pem > ${torplusworkspace}/ssl/${domain}.pem

## Pull docker image:

    docker pull torplusdev/production:ipfs_haproxy-latest

## Run docker container:

    cd ${torplusworkspace}
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ # set your seed
    nickname=tum33212 # set your nickname
    http_address=1.1.1.1:80 # set your webserver ip/name. the port :80 must be entered after ip or domain

##  Run docker:
    docker run \
            --name torplus \
            -e nickname=${nickname} \
            -e seed=${seed} \
            -e role=hs_client \
            -e HOST_PORT=80 \
            -e PP_ENV=prod \
            -e http_address=${http_address} \
            -p 80:80 \
            -p 28000:28080 \
            -v ${PWD}/tor:/root/tor \
            -v ${PWD}/ipfs:/root/.ipfs \
            -v ${PWD}/ssl:/etc/ssl/torplus/ \
            -v ${PWD}/hidden_service:/root/hidden_service \
            --add-host host.docker.internal:host-gateway \
            --rm \
            torplusdev/production:ipfs_haproxy-latest

## Add text record to DNS:

    cat ${torplusworkspace}/hidden_service/hsv3/hostname
 
    torplus=<onion address without .onion suffix>

#To check TXT record you can use 
https://www.whatsmydns.net/#TXT/torplus.{domain}

## Add torplus subdomain to your domain

    In the domain manager you need to add forward subdomain of torplus to https://torplus.com
    
# Сhecking sample server

## Install TorPluse client

    1. Go to the site https://torplus.com/download/
    2. Select the installer for the OS (example "Download for Windows (10+)")
    3. Run installer.
    4. Follow the instructions of the installer.
    5. Start or restart (if open) Сhrome browser.
    6. In the browser message "Torplus extension add" click on the button Enable extension.
![image](https://user-images.githubusercontent.com/52072466/144256915-900f09a6-fb4f-4e52-ae57-55a3b9ce6900.png)
    
    7. Go to https://torplus.{your domain name}  # There is a successful transition to the site https://torplus.{your domain name}
    8. Сlick on the Torplus extension with the left mouse button and switch the switch position to off mode.
![image](https://user-images.githubusercontent.com/52072466/144257475-42afcdba-4e73-4ce2-aa27-adb83cfeedea.png)
    
    9. Go to https://torplus.{your domain name}  # There is a redirect to the page for requires the Torplus application https://torplus.com/requires/


