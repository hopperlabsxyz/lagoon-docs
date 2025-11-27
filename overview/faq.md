---
description: Find below the answer(s) you are looking for.
---

# FAQ

## General questions

<details>

<summary><strong>What is a Lagoon vault?</strong></summary>

A Lagoon vault is made of:&#x20;

* 1 vault's (ERC-7540) smart contract;
* 1 silo contract which contains pending deposits and withdrawals.
* A curation solution, such as a Safe.

</details>

<details>

<summary>What do users get when they deposit in a vault?</summary>

In exchange of their assets, users receive a share of the vault, in the form of an ERC20.

</details>

<details>

<summary>Is the share of the vault compatible with the rest of DeFi ?</summary>

Yes all shares are ERC20 token.

</details>

<details>

<summary><strong>How many chains are supported?</strong></summary>

Chains list is available at [networks-and-addresses.md](../resources/networks-and-addresses.md "mention")

</details>

<details>

<summary><strong>Can the curating solution/address be updated? (safe variable in the smart contract)</strong></summary>

No, it can't be updated.

</details>

<details>

<summary><strong>What types of strategy can be set up?</strong></summary>

It depends on the curation solution you chose and its flexibility.

</details>

<details>

<summary>Does the protocol charge fees ?</summary>

Lagoon has the capacity to activate a fee switch at the protocol and vault level. Those are capped at 30% and are taken on the fees of the curator. Currently no vault is subject to fees.\
\
&#xNAN;_&#x45;xample:_\
_A curator generates 1000 shares of fees thanks via management and performance fees. The protocol fee rate is 10% for this vault. Lagoon will receive 100 shares and the fee receiver 900 shares._

</details>

<details>

<summary>Can a vault do cross-chain strategies ?</summary>

The strategies a curator can do is exclusively limited by the curation solution he used.&#x20;

For example, a vault curator using a Safe can bridge funds across EVM chains, non-EVM chains, and even CEXs.&#x20;

</details>

## Security

<details>

<summary><strong>How decentralized is the protocol?</strong></summary>

For now, vault deployed via the factory are upgradeable by Lagoon. Some of the first vaults deployed are upgradeable by the curator.

</details>

<details>

<summary><strong>Is the protocol open-source?</strong></summary>

Yes all contracts are verified on Etherscan.

</details>

<details>

<summary><strong>Has Lagoon been audited?</strong></summary>

Yes several iterations of the vaults have been audited by Nethermind, find out more [here](../resources/audits.md).

</details>

