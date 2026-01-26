---
description: Learn about Lagoon's technical terms
---

# Terminology

## Vaults

**Vaults are smart contracts responsible for tokenizing the ownership of a strategy in exchange of an underlying asset.** Vaults manage deposits, withdrawals, and accounting for the underlying asset.

## ERC-7540

ERC-7540 vaults introduce **asynchronous deposits and withdrawals**, allowing vault entry and exit to be processed in a 2 steps fashion, one for the request and one for the claim. It is also used for share tokenization in ERC20. Lagoon vaults are based on this standard.

## Multisig Wallet

An 'Externally Owned Account' (EOA) is controlled by a single private key. While a multisig wallet is built on top of a smart contract and requires several 'EOA' acting as signers for each transaction.

A multisig wallet emphasizes security and programmability. This solution is suitable when multiple parties need to coordinate.

{% hint style="success" %}
In this field, [Safe](https://app.safe.global/) is the most prominent and battle-tested multisig wallet provider.
{% endhint %}

## Valuation oracle

The vault is expecting to receive updates of the total value of vault in order to settle requests. The price oracle is responsible for computing this value and propose it. For this value to be enforced in the vault and used to settle requests the curator address has to approve it.

## Underlying token

The **underlying token** refers to the asset in which users will deposit and withdraw.&#x20;

The valuation oracle will propose the new total asset in this unit of account.

## Shares

**Shares** represents a claim on assets deposited within the vault.

They are distributed in exchange of a given underlying token, at a price determined by the valuation oracle, at settlement.

While the **price per share** reflects a given vault's performance and its evolution over time denominated in the vault underlying token.

## **Vault** Settlement

**Settlement** describe the action by which a curator validate the previous valuation oracle proposal. A settlement will honor all deposits requests. Withdrawal requests will be honored if the curation address has enough assets available.
