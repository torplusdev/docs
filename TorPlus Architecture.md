App flow

https://miro.com/app/board/o9J_lSGHzng=/



Open source infrastructure modifications



**Tor customization**

Modifications to tor are focused in two key areas - payment-related and performance optimizations.
Tor was modified in order to enable transmitting payment related data between the client and one of the circuit nodes. Payment data can travel from a client node to one of the circuit nodes, and back from circuit nodes to client. These data exchanges serve to transmit payment requests, operations that are a part of payment processing and event indications (e.g. payment complete event).

**IPFS customization** 

There are three main areas where IPFS implementation was extended for torplus functionality:

1. Tor routing support.
   IPFS supports multiple transport providers, however it does not support onion routing (TOR) out of the box. We have implemented integration with tor by using tor SOCKS proxy functionality and the ability to use control port in order to control tor runtime from an external application.
   *go-libp2p-onion-transport* project is responsible for providing tor transport functionality and it relies on external tor executable to provide it.
   Since IPFS assumes ip routing in many places, multiple projects had to be modified to support onion addressing besides the actual transport     implementation (mainly in *go-libp2p-kad-dht* and related projects)

2. Integration with PaymentGateway and requests for payment
Requests for payments are generated and sent by bitswap engine (*go-bitswap* project) based on the traffic transmitted per remote peer.
Bitswap implementation was modified to track the sent data and once a threshold is reached, it uses PG to send a payment request back to the peer. 
If payment isn't performed, then the transmission to that peer is throttled.

3. CID frequency management and content distribution functionality
In order to enable ipfs service providers save a percentage of available storage space to be used by frequently requested content items, a frequency tracking submodule was implemented so that CID requests are monitored and for top N (configurable) requested CIDs, the statistics are     maintained and can be requested by adjacent nodes.
This is implemented by a new ipfs command (ipfs fill) that queries the known peers for top CIDs and then uses this data to fill a predefined part of available storage.



Domain name resolution



**Web resource publishing in TorPlus**

In order to use any web resource via torplus, it needs to be made available to TorPlus network and integrate with key infrastructure elements, such as PaymentGateway and ipfs. At the moment, this integration is achieved by using a docker container to run the bridging module that provides all the required interfaces. The reference deployment architecture contains two docker containers: one is the torplus bridging module that provides the necessary infrastructure  and the second container hosts the web server with the web site.

The following are the steps required to publish a website on TorPlus network:

1. Purchase a dns address for the domain. 
   You will need to prove domain ownership later on to generate SSL certificate, and also dns control is required to set up torplus domain resolution.
2. Add a subdomain torplus.<your domain>
   This is the domain that will be used externally to reach the website.
3. Get a SSL certificate for your web resource and place the certificate into ssl_torplus folder
4. place your nginx hosted web sited sources into www folder
5. Get a stellar seed (secret key) for your account (via API/installer) and execute the following command:

- 

docker run --rm \

  -e seed={seed} \

  -p 80:80 \

  -p 8000:8000 \

  -e PP_ENV=prod \

  -v $(PWD)/www:/opt/www \

  -v $(PWD)/tor:/root/tor \

  [torplusserviceregistry.azurecr.io/private/haproxy_hoster:latest](http://torplusserviceregistry.azurecr.io/private/haproxy_hoster:latest)



After starting up, you will see an onion address in the console output. 
This is the hidden service address for your web resource, save it for further configuration.

6. At the website DNS management API, add a TXT record that contains the following string "torplus=<onion address>"

   Where <onion address> is the hidden service address save at the end of the previous step.