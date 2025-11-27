---
hidden: true
---

# How to setup Safe & Zodiac Roles Modifier

## **Safe**

You can deploy a Safe using the [Safe{Wallet}](https://app.safe.global/) user interface. This is the most straightforward approach to setting up new custody for your vault.

Another option is to deploy new Safe wallets using the [Safe CLI](https://docs.safe.global/advanced/cli-demos), which is useful if you plan to automate your vault deployment pipeline.

{% embed url="https://vimeo.com/1083954125/c4e40e9ce3" %}

{% embed url="https://vimeo.com/1083954155/c71b069a63" %}

## Zodiac Roles Modifier

The [Zodiac Roles Modifier](https://docs.roles.gnosisguild.org/) is an on-chain permissions module for [Safe {Wallet}](https://safe.global/).  It enables customizable roles with specific permissions. These permissions define the ability to interact with specific smart contract addresses, functions, and allowed arguments. For example, a permission could specify that the Roles Modifier is only allowed to execute a transfer of up to 50 gwei of an ERC-20 token to a specific address.

The most straightforward way to add a Roles Modifier module to your Safe is through the "Apps" section of the Safe web app. Using this method, the default owner of the Roles Modifier will be the Safe itself. The owner is responsible for managing permissions and assigning role members. If you require a more complex setup, you can transfer ownership to another address.

{% embed url="https://vimeo.com/1083954183/aa5e19e1ab" %}

{% embed url="https://vimeo.com/1083954209/2881b88dcb" %}

## Sending Transactions

There are two main ways a Safe wallet can send transaction to the outside world.&#x20;

\
First, through the configured multi-sig. It's an N-out-of-M signature scheme that only allows a transaction to be sent if the predefined threshold is reached.

The other way is through a [Safe module](https://docs.safe.global/advanced/smart-account-concepts) (such as the Zodiac Roles Modifier).

Both methods are natively supported in the official Safe web app. Depending on the signer you are connected with, it will automatically suggest the appropriate route.\
\
Another, more scalable, approach is to use [Zodiac Pilot](https://www.zodiac.wiki/documentation/pilot-extension):

> Zodiac Pilot is a browser-based tool for Safe accounts that lets owners and trusted delegates build and execute multi-step transactions across multiple dapps. It combines modular batching, [programmable permissions](https://www.zodiac.wiki/documentation/roles-modifier), and a built-in sandbox environment to simulate transactions before they’re sent onchain.

## DeFi-Kit Support

The [Karpatkey's DeFi-Kit](https://github.com/karpatkey/defi-kit), is a collection of permissions build for specific protocol. It can helps you getting quickly started for supported protocols.



