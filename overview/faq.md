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

Users receive ERC20 tokens, representing a share of the vault, in exchange for their assets.

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

This feature is not currently available but will be included in a future update.

</details>

<details>

<summary><strong>What types of strategy can be set up?</strong></summary>

It varies depending on the chosen curation solution and its adaptability.

</details>

<details>

<summary>Does the protocol charge fees ?</summary>

Lagoon has the capacity to activate a fee switch at the protocol and vault level. Those are capped at 30% and are taken on the fees of the curator. Currently no vault is subject to fees.\
\
&#xNAN;_&#x45;xample:_\
_A curator generates 1000 shares of fees through management and performance fees. The protocol fee rate is 10% for this vault. Lagoon will receive 100 shares and the fee receiver 900 shares._

</details>

<details>

<summary>Can a vault do cross-chain strategies ?</summary>

The strategies a curator can execute are exclusively limited by the curation solution he used.&#x20;

For example, a vault curator using a Safe can bridge funds across EVM chains, non-EVM chains, and even CEXs.&#x20;

</details>

## Security

<details>

<summary><strong>How decentralized is the protocol?</strong></summary>

Lagoon is the only entity capable of deploying vault upgrades; however, enforcement of these upgrades is exclusive to each vault's "Upgrade Authority."

</details>

<details>

<summary><strong>Is the protocol open-source?</strong></summary>

Yes all contracts are verified on Etherscan.

</details>

<details>

<summary><strong>Has Lagoon been audited?</strong></summary>

At Lagoon, we prioritize security and focus on delivering high-quality code with thorough tests and audits. The several iterations of the vaults have been audited by Nethermind. Discover more [here](../resources/audits.md).&#x20;

</details>

