# Vault valuation

In Lagoon, the valuation of the vault is injected into the smart contract by the [Valuation Oracle](roles-and-capacities/valuation-oracle.md). The accuracy of this value is crucial since it will determine:

* the amount of shares a user will get.
* the amount of assets a user will get at redemption.
* the amount of performance fees taken.

For greater security, a vault valuation requires a second step: the curator must approve the valuation proposed by the Valuation Oracle. Learn more  [here](how-to/update-the-vault-valuation-and-settle-requests.md).

<details>

<summary>Why a 2-steps procedure ?</summary>

Valuation is a critical factor in ensuring the vault's correct operation. To support this, it’s essential to give vault administrators flexibility in their valuation process. This 2-steps approach offers several benefits:

* **Customizable address selection** – Admins can choose relevant addresses for valuation.
* **Curator control limits** – Reduces excessive curator influence.
* **Enhanced oversight** – Increases the number of entities required to review valuation proposals.

</details>

The valuation must exclusively contain the positions held on the curator address at the moment of the call. Consequently, vault valuation <mark style="color:red;">**must not**</mark> take into account the pending requests.&#x20;

Another consequence is that the first valuation update will be at 0.

{% hint style="info" %}
The valuation can be computed off-chain but it can also be based on a smart contract responsible to fetch  the data on-chain.
{% endhint %}

You can find how to update the valuation [here](how-to/update-the-vault-valuation-and-settle-requests.md).
