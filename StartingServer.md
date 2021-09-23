# Starting a TorPlus server

TorPlus uses the Stellar blockchain, which means you will need to set up a Stellar Lumens (XLM) address to pay and get paid on TorPlus. In addition, for an address to be “active” on Stellar, it must maintain at least a 1 lumen balance (currently $0.28).

There are several Stellar wallets you can use. See https://www.stellar.org/lumens/wallets. Once you create a wallet, you will need to deposit 1 lumen into it to activate it.

Here’s an example how to create a new XLM wallet on the web based StellarTerm (https://stellarterm.com/):
1.	Go to https://stellarterm.com/ and choose “New Account” and follow the instructions to create a new account. Write down the secret & public keys.
2.	You will now need to deposit funds into this account. You can buy XLM directly via StellarTerm using a credit card, or you can use an exchange such as LetsExchange (https://letsexchange.io/) to convert BTC, ETH or some other coin

You can either use this account directly with TorPlus. Alternatively, you can create an account for each TorPlus server and transfer any gains from the servers to the “main” account periodically using TorPlus Tools (https://github.com/torplusdev/torplus_tools).

Assuming `SECRET` is the secret key for your main XLM account (the one we've just generated above):

```shell
$ docker run -rm -v tpconf:/var/torplus/conf SECRET torplus_create_server_conf
```

This will:
- Create a new Stellar wallet
- Activate it by paying 2 lumens from `SECRET`
- Enable it to use TorPlus coins
- Store the seed for the new wallet in the `tpconf` Docker volume

Note that `SECRET` is *not* stored.

Next, to start a TorPlus web server rooted at `$(PWD)/www`, run:

```shell
$ docker run --rm -p 80:80 -v $(PWD)/www:/opt/www -v tpconf:/var/torplus/conf torplus_web_server
```

