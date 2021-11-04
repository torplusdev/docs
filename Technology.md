# Technology

    TorPlus is built on top of Tor, and provides the same anonymity guarantees as Tor, but extends it in two ways:

## Monetization layer.

    Tor relies on volunteers. Servers and bandwidth are donated. The only incentive for someone to operate a Tor server is charity. Unfortunately, this means few resources: today there are about 6000 Tor relays. For comparison, Google has about 1 million servers. TorPlus makes deploying relay servers profitable, which in turn means faster network.

    In addition, the TorPlus protocol pays content providers (i.e. web sites), encouraging creation and sharing of new content, without the need to rely on tracking ads.

## Caching layer.

    TorPlus incorporates IPFS for distributed storage and caching. This means web sites hosted on TorPlus can concentrate on creating great content. Storage providers are also given incentive via monetization layer, resulting in faster and easier access.

## The gory details.

    TorPlus relies on Stellar-based tokens (TorPlusToken = TPT) to pay for bandwidth, storage and content. These tokens are delivered to service and content providers and used to compensate them for the high quality services they provide.

    TorPlus Clients automatically receive a monthly allocation of tokens from the TorPlus Authority as part TorPlus subscription, which are sufficient for covering that month’s usage. In order to experience TorPlus, you can register for a trial and get some tokens for free, just by downloading the torplus client app here.

## Payments and anonymity

    When a client accesses the TorPlus network, it accesses a TorPlus “guard” node, similar to standard Tor. However, as part of the TorPlus protocol, the client maintains a small token “balance” with the guard. In turn, nodes pay (transfers tokens) when they receive a service from other nodes.

    As the client only maintains a balance with guard nodes, payment is decoupled from access to content and effectively are anonymous. No one (including the guard) can link between payments by clients, the end recipient of the payment and the content provided back to them.

## Caching

    TorPlus incorporates IPFS as distributed storage and cache layer.

    To maintain anonymity guaranties, all access to IPFS is done over the TorPlus protocol, with clients paying for accessing content on IPFS in addition to paying the relays.

    By default, IPFS nodes implement a caching protocol: IPFS nodes automatically fetch popular content on the network. This improves the network performance, while also making running an IPFS node more profitable.

## Client-Server Sequence:

    1. Client establishes TorPlus connection via 3 random nodes (Guard node -> Relay node -> Exit node).
    2. Client sends all data via encrypted Tor “onion” messages.
    3. The guard decodes the outermost layer and forwards the message to the next node on the chain, until the message gets to the exit node and forwarded to the end recipient (any internet server) in an non-encrypted form.
    When data is received by the client, the same steps happen but in reverse order.
    4. When the total amount of data sent via TorPlus goes over a threshold (50MB), each node send a payment request back to the client. These payment requests are send using tor and are anonymous, the nodes (except the guard node) do not know the client identity.
    5. On receiving payment requests, client performs the actual payment of TPT tokens to each tor node. The payment is done using the tor network as well, so that the payment itself is also anonymous and does not disclose client identity.
    The nodes receive an indication of the payments made and communication continues seamlessly until the next payment cycle is reached and then the same process is repeated.