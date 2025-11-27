---
description: This page will guide you through activating synchronous deposits.
---

# Activate and manage a synchronous vault

{% hint style="danger" %}
Synchronicity is an **advanced feature.** Please read this page entirely before activating synchronous deposits on your vaults.
{% endhint %}

{% hint style="warning" %}
This feature is not available on Mantle.
{% endhint %}

## How do synchronous deposits work?

By giving a `lifespan` to your vault valuation, you allow the vault to process deposits.\
The amount of shares received is computed using the price per share at the last valuation update. When `syncDeposit()` is available, the `requestDeposit()` function is disabled. Redemptions continue to be processed asynchronously.



## Arbitrage and the danger of synchronous deposits

Most Lagoon Vaults rely on offchain oracles to know their valuation. Since this value is not continuously assessed, it might differ from reality with time.

For example 2 days after a valuation update, the fair valuation might have increase while it's registered value in the vault is still the same, such situation would create an arbitrage opportunity at the expense of shares holders.

We advice vault curator and administrators to be **extremely careful with this feature**.



## How to activate synchronous deposits ?

First give your totalAssets variable a lifespan. This value, expressed in seconds, determines for how long users will be able to call syncDeposits. To update it, call as the `safe`  role:

```solidity
function updateTotalAssetsLifespan(uint128 lifespan) onlySafe;
```

| Variable | Definition                                                               |
| -------- | ------------------------------------------------------------------------ |
| lifespan | Duration in seconds during which the valuation will be considered valid. |

{% hint style="danger" %}
The lifespan value can't be too close to uint128.max because it might cause an overflow. If you want to allow sync deposits for an "unlimited" amount of time, you can put: `31536000000` seconds, roughly \~1000 years.
{% endhint %}

Now [update the valuation of your vault](update-the-vault-valuation-and-settle-requests.md) as usual. After the call to settleDeposit or settleRedeem, users will be able to do synchronous deposits.





## How to update the valuation while totalAssets is valid and how to go back to asynchronous mode ?

It is not possible to propose a new valuation while the current one is considered valid (see point above).\
To update the valuation, the `safe` role first has to call:

```solidity
function expireTotalAssets() onlySafe;
```

After this call, `isTotalAssetsValid` will return false and the `Valuation Oracle` and the `safe` can follow the standard [update-the-vault-valuation-and-settle-requests.md](update-the-vault-valuation-and-settle-requests.md "mention") procedure.

