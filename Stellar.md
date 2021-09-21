**TorPlus Stellar integration**

TorPlus uses Stellar blockchain for microtransactions and managing payments in the system.
Stellar native currency is XLM (Lumens) and XLM balance is required for every live account.
Besides providing fees



![Stellar modules](./images/stellar modules.png)



Stellar account is described using a seed (private key) and an address (public key).
Seed should stay a secret, since it allows to make changes to the account - including payments to any account.
**Public address** is the one you can use for accepting payments, or publicly identifying your account.
Public address can be restored from the seed if you forget it, but no the other way around. 
Seed can not be restored if lost, effectively losing the account.

To use a stellar account with TorPlus it needs to be able to make and accept payments in TorPlus currency - "pptoken" assets.
A stellar asset is defined using two parameters, asset code and issuer.
For pptoken asset the code is "pptoken" and the issuer address is "GDTY7EETPKH2EY3NDNB3AZO2KTOSLSNHH7E3VM6C6B523J3Q5ZLLNK46".
Once you set up pptoken currency for your account (Called setting up a trust line), you can receive and make payments in pptoken currency.

If you feel comfortable with Stellar, you can make the neccessary changes to your account yourself, but if not - you can use helper scripts.
To use the helper scripts, clone the following repo: https://github.com/torplusdev/torplus_tools
Providers folder requires the scripts needed to set up account for a TorPlus service provider.

Since Stellar accounts have minimum balance (in native XLM currency), you will need an account that will be able to fund (transfer at least 2 XLM) to your new account.
You should have the funding account seed available for the next operation.

To create a new account that you can use for TorPlus, call *create_and_fund.py* passing the funding account seed as a parameter.
It will output the seed and address for the newly created account. 
***Make sure to save this information, it will not be shown again and will void the account - if lost.***

Next, run *setup_account.py*, passing the seed of the new account (printed by the previous operation). setup_account will set up the trustline for the "pptoken" currency.

If you wish to test that everything was set up correctly, you can run *validate.py* passing your account *address* as a parameter.

***Note:*** If you already own a Stellar account, and you wish to use it as-is for TorPlus - you can skip creation of a new account and just run setup_account with the existing account seed.
We recommend creating a separate account for using with TorPlus, even if you feel comfortable with Stellar and already have one or more Stellar accounts.

