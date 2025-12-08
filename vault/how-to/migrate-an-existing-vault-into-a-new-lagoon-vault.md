# Migrate an existing vault into a new Lagoon vault



This tutorial will explain how to migrate an existing vault or fund into a Lagoon vault without needing an action from your shareholders.

{% hint style="danger" %}
Migrating a vault is a dangerous procedure, proceed if you know what you are doing.
{% endhint %}



Prerequisites:

* having the capacity to approve and deposit the assets of the deprecated vault into the future Lagoon vault.
* having all assets funds in underlying. Eg: in a USDC vault you should only have USDC.
* the deprecated vault shares should not be used in DeFi.



{% hint style="info" %}
It is still possible to do a migration with partial or no assets in the underlying but it might no be adapted for every situation. If you must do it, please reach out [here](https://tally.so/r/nGBGyz).
{% endhint %}

This tutorial assumes that you have a clear understanding of how [Lagoon's vault valuation works.](../vault-valuation.md) To showcase the migration process, we will use a USDC vault as an example.



## Step1: Pause the deprecated vault and take a snapshot of its shares repartition

We advice to pause the vault or at least freeze movement of shares to avoid loss of funds from your users.

In our case, here is the result of our snapshot:

<table><thead><tr><th width="249">address</th><th>amount of shares</th><th>% of the vault</th></tr></thead><tbody><tr><td>0x123</td><td>4</td><td>40%</td></tr><tr><td>0x456</td><td>6</td><td>60%</td></tr><tr><td>TOTAL</td><td>10</td><td>100%</td></tr></tbody></table>

_Concretely, the address 0x123 owns 4 shares out of the 10 total shares, which is equivalent to 40%._



## Step2: Create a Lagoon vault

[Follow this guide](/broken/pages/1uX1bXCjUHCCIjzAuoYl). In our case, it will be a USDC vault and its address is 0xabc.



## Step3: Deposit all the assets into the Lagoon vault

This is the tricky part of the procedure. When you will [update the valuation of the vault](update-the-vault-valuation-and-settle-requests.md) to accept your own deposit, you must input 0 as the valuation. If you input any other number, your vault shares accounting system will be broken and you will have to start again.

In our case, the total value of the first vault was 100 USDC, so we will do a deposit request of 100 USDC and [update the valuation](update-the-vault-valuation-and-settle-requests.md) at 0. Then, we claim our shares by calling the deposit function. Go over [Async Deposit Flow](../../developer-hub/integration/async-deposit-flow.md) if you need to know more about the deposit flow.

In our case, after the claim we received exactly 100 shares.



## Step4: Distribute the shares to the shares holders of the previous vault

You can distribute the shares using a dedicated tool. Be careful, frontend could be compromised, review the transactions carefully. We are going to use the [disperse.app](https://disperse.app) but if you are more confortable using blockchain explorers you can enter  `disperse.app` in etherscan to find directly the smart contract to interact with.

The address of the token you want to distribute is the address of the shares which is the address of the vault. In our case, 0xabc.

We will now distribute 40 shares to the address 0x123 and 60 shares to the address 0x456. In disperse.app frontend it will be something like this:



<figure><img src="../../.gitbook/assets/Screenshot 2025-05-19 at 12.21.06.png" alt="" width="375"><figcaption></figcaption></figure>

You can now submit and execute the transaction.

## Final notes:

* the Lagoon Vault performance metrics will start from 0, meaning that you will have to reconcile the deprecated vault and the vault history to have the full picture of your performance.
* the deprecated vault shares will be worth 0, meaning that you can't do a migration on a vault that is used in DeFi.
