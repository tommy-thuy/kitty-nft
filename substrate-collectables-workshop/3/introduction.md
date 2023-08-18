Introduction
===

At this point you have learned most of the core pieces of Substrate runtime development. However, our runtime is not very interesting as is.

This next section will teach you how you can add additional functions to your runtime to enable some of the most popular features of the original CryptoKitties game:

- Transferring a kitty
- Buying a kitty
- Breeding a kitty

To enable these features, we will need to interact with other modules like the `Balances` module from the SRML. We will also continue to work more with Substrate specific types.