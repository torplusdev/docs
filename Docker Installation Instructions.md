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
 docker run \\
    --name torplusipfs \\
    -p 28000:28080 \\
    -e nickname=${nickname} \\
    -e PP_ENV=prod \\
    -e seed=${seed} \\
    -v ${PWD}/tor:/root/tor \\
    -v ${PWD}/ipfs:/root/.ipfs \\
    -v ${PWD}/hidden_service:/root/hidden_service \\
    --rm \\
 torplusdev/production:ipfs-latest

## Add new files to ipfs

#Commands are executed in a new terminal with a running docker container on the computer

    torplusworkspace=<yourworkspacedir>
    cd ${torplusworkspace}/ipfs
    sudo mkdir -p ./data
    sudo cp ~/<the path to the file that we will upload to the ipfs network> /${torplusworkspace}/ipfs/data  # The file that we will upload to the ipfs is copied to the folder
    sudo docker exec -it torplusipfs /bin/bash
    /ipfs add ~/.ipfs/data/<file name>  # After successfully uploading the file to the ipfs, a message is displayed "added <cid> <file name>"

## Check uploaded files ipfs:

#Copy cid link from previous step
Open Chrome browser with TorPlus installed on your computer and follow the link https://torplus.wikitpdemo.com/ipfs/<cid>  # Playback or display of the file uploaded to the ipfs will start, if the file format is not supported by the site, then it will be downloaded to the computer. Attention!!! This verification will be charged for using the TorPlus network!

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

#install certbot:
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

##  docker run \
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
