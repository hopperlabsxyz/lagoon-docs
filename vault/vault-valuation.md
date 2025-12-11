# Vault valuation

In Lagoon, the valuation of the vault is injected into the smart contract by the [Valuation Oracle](roles-and-capacities/valuation-oracle.md). The accuracy of this value is crucial since it will determine:

* the amount of shares a user will get.
* the amount of assets a user will get at redemption.
* the amount of fees taken.

### How to compute a valuation ?

A new valuation proposal must exclusively take into account the positions held by the curating address. It mustn't include deposit and redemptions requests because they are not processed yet. They are pending outside of the vault, waiting to be settled.

Please see the following example:

| Value                | Amount                                       |
| -------------------- | -------------------------------------------- |
| Curating balance     | <mark style="color:$success;">10 USDC</mark> |
| Deposits Requests    | 5 USDC                                       |
| Redemptions Requests | 2 USDC                                       |
| Valuation expected   | <mark style="color:$success;">10 USDC</mark> |

We can see that the deposits requests are not taken into account here. The reason is because they are not processed yet.

In practice, the 10 USDC will be spread around different yield generating positions. It is the responsibility of the Valuation Oracle to properly value those different positions in USDC.

Consequently, the first valuation update will always be 0.

### A 2-steps procedure

For greater security, a vault valuation requires a second step: the curator must approve the valuation proposed by the Valuation Oracle.

<details>

<summary>Why 2-steps ?</summary>

Valuation is a critical factor in ensuring the vault's correct operation. To support this, it’s essential to give vault administrators flexibility in their valuation process. This 2-steps approach offers several benefits:

* **Customizable address selection** – Admins can choose relevant addresses for valuation.
* **Curator control limits** – Reduces excessive curator influence.

</details>

{% hint style="info" %}
The valuation can be computed off-chain but it can also be based on a smart contract responsible to fetch  the data on-chain.
{% endhint %}

You can find how to update the valuation [here](how-to/update-the-vault-valuation-and-settle-requests.md).
