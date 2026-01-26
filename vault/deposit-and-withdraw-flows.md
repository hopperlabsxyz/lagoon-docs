---
description: >-
  Lagoon vaults' deposit and withdrawal flows are based on an asynchronous
  mechanism.
---

# Deposit and Withdraw flows

### Deposit flow

<figure><img src="../.gitbook/assets/Screenshot 2025-05-01 at 16.20.53.png" alt=""><figcaption><p>Deposit workflow</p></figcaption></figure>

#### 1) Request Deposit

Users manifest their desire to enter a vault by calling `requestDeposit`. During this operation, users' assets are transferred to the pending silo.

{% hint style="info" %}
The pending silo is a utility contract deployed at the creation of the vault to store the pending assets of the requests. It is used to simplify the vault’s internal accountability. It is also used for redemptions requests.
{% endhint %}

#### 2) Valuation update and settlement

To settle the requests, the vault needs the fair valuation of its various positions. In Lagoon, this information is injected into the smart contract by the [Valuation Oracle](roles-and-capacities/valuation-oracle.md).&#x20;

For more security, it is a two-steps procedure:

* First, the valuation oracle will propose the valuation by storing it in the vault.
* Then, the curator can accept this valuation by calling `settleDeposit`.&#x20;

If there are pending deposit requests, they will be settled and the corresponding shares are minted.



3\) Claim the shares

After the settlement, users start earning directly. Their ownership of of the vault is represented by an ERC20 token, named the share. Shares are waiting inside the vault and can be claimed anytime. To get the exact position of a user you can follow this tutorial [Get a user position](../developer-hub/integration/get-a-user-position.md).

{% hint style="info" %}
It is possible for the curator to claim the shares on behalf of the users. In this case, the shares will be transferred to the address of the user. You can find how [here](how-to/claim-shares-on-behalf-of-users.md).
{% endhint %}

### Redemption flow

The redemption flow is the same as deposit one with some little differences:

* Users can't cancel their withdraw requests. This is to prevent users from making curator unwind positions for nothing.
* &#x20;Curator can't claim on behalf of users and distribute back the assets. The user must claim them.<br>

<figure><img src="../.gitbook/assets/Screenshot 2025-05-01 at 16.21.12.png" alt=""><figcaption><p>Redemption workflow</p></figcaption></figure>

