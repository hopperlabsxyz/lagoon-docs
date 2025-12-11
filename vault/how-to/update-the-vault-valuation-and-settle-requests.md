# Update the vault valuation & settle requests

## The first Valuation update

{% hint style="danger" %}
Before updating the valuation of a vault, please read how it works [here](https://app.gitbook.com/o/lCXvsIGfJAYmkImyThoD/s/5vcVbvvRrtwZ0uaFrufF/vault/how-to/update-the-vault-valuation-and-settle-requests#update-the-vault-valuation).
{% endhint %}

If it is your first valuation update, the input value will be 0, regardless of the fact that there are pending deposit requests. If you don't understand why, please check the documentation on how the [Vault Valuation works](../vault-valuation.md).

## Update the vault valuation

In Lagoon, the valuation of the vault is injected into the smart contract by the [Valuation Oracle](../roles-and-capacities/valuation-oracle.md).

For more security, it is a two-step procedure:

* First, the Valuation Oracle will store its valuation proposal in the vault.
* Then, the curator can accept this valuation by calling either `settleDeposit/settleRedeem`. Note that they are not exactly equivalent; you can learn more [here](update-the-vault-valuation-and-settle-requests.md#settledeposit-vs-settleredeem).

To access the management page, please check this [tutorial](../create-your-vault/access-your-vault-on-lagoon.md).

{% hint style="danger" %}
The first valuation injected must be 0. Please read how the valuation works [here](../vault-valuation.md).
{% endhint %}

### Current vault state

On this page, you can see the current state of your vault.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-25 at 19.18.14.png" alt=""><figcaption><p>Current vault state</p></figcaption></figure>



#### Simulation mode & propose valuation

By touching the input bar, you will enter the simulation mode. It will display in green the new vault state if the vault valuation was effective.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-25 at 19.23.42.png" alt=""><figcaption><p>Vault state with a new valuation</p></figcaption></figure>

Click `Propose new valuation` button to initiate the transaction.

### Approve valuation & settle

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-28 at 14.40.03.png" alt=""><figcaption><p>A vault ready to be settled by the curator</p></figcaption></figure>

After the Valuation Oracle proposed the new valuation, the curator is in a position to validate it.

Let's have a look at the information displayed.

* **Total assets and new total assets -** here we can see a valuation update from \~2,508,115 USD0++ to \~2,508,330 USD0++.&#x20;
* **Calculated Fees -** Since the new valuation is lower than _Asset at the high-water mark_, no performance fees will be taken, and the high-water mark will remain unchanged. On the other hand, management fees are taken.
* **Requests** - There for \~30,460 USD0++ ready to be deposited and \~624.94 ustUSR++ (vault shares) ready to be redeemed. To redeem the shares, the vault will use the assets being deposited and those present in the curator's address. That's why the section _Underlying to Unwind_ is empty; there are enough pending deposits to settle the redemptions.
* **Settle with deposits** button - See next section.&#x20;

#### SettleDeposit vs SettleRedeem

Approving the valuation passes by two possible functions: `settleDeposit` and `settleRedeem`. Here is a table enumerating each function behavior:

| Behavior                                       | settleDeposit | settleRedeem |
| ---------------------------------------------- | ------------- | ------------ |
| check for consistency in the valuation updated | ✅             | ✅            |
| take fees                                      | ✅             | ✅            |
| settle deposit requests                        | ✅             | ❌            |
| try to settle redemption requests              | ✅             | ✅            |

{% hint style="info" %}
Why does the _settleDeposit_ operation try to settle redemption requests?

That is because the best moment to honor redemption requests is when there is a maximum of underlying in the curator address, it is the case after a settlement of deposits.
{% endhint %}

{% hint style="info" %}
Why do the settlement operations only **try** to settle redemptions?

In order to improve the curator's flexibility, the settlement of redemptions will only happen if there are enough assets in the curator's address. It is the curator's job to make sure it is the case.
{% endhint %}

{% hint style="info" %}
Can I update the valuation if there are no deposit/redeem requests?

Yes, you can, everything will work the same.
{% endhint %}

