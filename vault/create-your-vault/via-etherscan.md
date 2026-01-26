---
description: Step-by-step instructions to create vault.
---

# Via Etherscan

{% hint style="success" %}
You can find the supported chains and factories addresses [here](../../resources/networks-and-addresses.md). You can find a telegram support link at the bottom of this page :arrow\_down:
{% endhint %}

## **Lagoon Vault Factory**

The **Lagoon Vault Factory** is the core smart contract that enables permissionless deployment of on-chain Lagoon Vaults. It allows anyone to create a fully customizable vault across supported chains.

{% hint style="warning" %}
Before launching a vault, feel free to deploy a test vault on Arbitrum using the following Factory [here](https://arbiscan.io/address/0xb1ee4f77a1691696a737ab9852e389cf4cb1f1f5#writeProxyContract#F1).
{% endhint %}

{% hint style="info" %}
On HyperEVM, please use big blocks to avoid out-of-gas reverts. You can switch [here](https://hyperevm-block-toggle.vercel.app/).
{% endhint %}

## Vault Deployment Function: `createVaultProxy`

`createVaultProxy` is the function used to deploy a new Lagoon Vault from the Factory contract.

{% hint style="danger" %}
Etherscan based scan have some bugs. Put double quotes around your addresses and strings.\
Like this: "0x0000000000000000000000000000000000000000"
{% endhint %}

{% hint style="info" %}
The address used to create a new vault won't have any roles in the vault, unless specified in the arguments. In other words, you can use any address to deploy a vault.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-21 at 16.19.54.png" alt=""><figcaption></figcaption></figure>

## **Create Vault Proxy Parameters**

### Upgradability parameters

**`_logic`** (address) – This is the address of the logic of your contract. Your vault is a proxy that will use another contract logic. If you don't want to use the default version of the vault, you can supply the address of another authorized logic. If you want to use the default version, put the address 0.

{% hint style="success" %}
To use the default vault version, put:

```
0x0000000000000000000000000000000000000000
```
{% endhint %}

**`_initialOwner`** (address) – The address that will be capable of upgrading the vault version. This address will be the owner of the ProxyAdmin, a contract deployed at the proxy construction. If you want your vault to be immutable, you can renounce the ownership of this ProxyAdmin contract by calling `renounceOwnership`(). <mark style="color:red;">This action is irreversible.</mark>



**`_initialDelay`** (uint256) – Delay required to wait to update a vault. Expressed in seconds. The minimum value is `86400` seconds (24 hours). The max value is `2592000` seconds (1 month). You can edit this delay, again after waiting the delay itself.

### Underlying Asset

**`underlying`** (address) – The address of the underlying asset (ERC20 token). This is the token users will deposit.

The Valuation Oracle (valuationManager) will be providing the valuation of the vault in this unit of account.

{% hint style="danger" %}
This token can not be a rebasing token such as stETH or Aave debt token. In the case of stETH you would use it's non-rebasing equivalent, wstETH.
{% endhint %}

***

### Vault General Parameters

**`name`** (string) – The name you want to give to the ERC-20 shares. Immutable



**`symbol`** (string) – The ERC-20 shares symbol. Immutable.



**`enableWhitelist`** (boolean) – Restrict vault access to approved addresses. Put `0` for `false` and `1` for `true`. Can be enabled during initial setup only.

{% hint style="warning" %}
Whitelist can only be activated at vault creation. Deactivation can happen anytime and is irreversible.&#x20;

If activated, the list itself can be edited anytime.
{% endhint %}

{% hint style="success" %}
If the whitelist is enabled, you can add and revoke addresses using`addToWhitelist` and `revokeFromWhitelist`.
{% endhint %}

***

### Roles & Governance Setup



**`safe`** (address) – Curation address that holds the vault’s assets. It is referred as curation address in the doc. This variable is immutable. Any curation solution is compatible including:

* **Safe Multisig** – [Safe {Wallet}](https://safe.global/)&#x20;
* **MPC - Multi-Party Computation** wallet&#x20;
* **EOA - Externally Owned Account** wallet&#x20;

{% hint style="success" %}
This is the address that will manage the vault positions and validate Valuation.
{% endhint %}



**`admin`** (address) – Address with administrative privileges. The [Vault Admin role](../roles-and-capacities/vault-admin.md) is responsible for managing the governance structure that will rules the vault. The **admin** has significant control over the vault's key parameters and security features, defining how it operates and is governed.



**`valuationManager`** (address) – [The Valuation Oracle role](../roles-and-capacities/valuation-oracle.md) provides updated valuations of the vault total assets.

{% hint style="info" %}
Can be a **multisig** (manual updates) or a **smart contract**.
{% endhint %}



**`whitelistManager`**  (address) – [The Whitelist Manager role](../roles-and-capacities/whitelist-manager.md) controls which addresses are authorized to interact with the vault.

{% hint style="warning" %}
If `enableWhitelist` is `false`, please provide a null address value :

```shellscript
0x0000000000000000000000000000000000000000
```
{% endhint %}

***

### Fees Parameters Setup

**`feeReceiver`** (address) – Address where fees will be sent at each settlement.

**`managementRate`** (uint16) – Percentage of fee charged for vault operation, express in **basis points**.

**`performanceRate`** (uint16) – Percentage of fee charged on performance gains, express in **basis points**.

{% hint style="info" %}
💡 Fees are expressed in **basis points (bps)**

* `0.5%` = `50 bps`
* `2%` = `200 bps`
* `15%` = `1500 bps`
{% endhint %}



**`rateUpdateCooldown`** (uint256) – Amount of time before a fee rate update takes effect (in second). Immutable.

{% hint style="info" %}
💡 Value is in **seconds** (e.g., `3600` for **1 hour**).
{% endhint %}

***

### Technical Parameter

**`salt`** (bytes32)  – A unique value in bytes32 to used in conjunction with the vault's other parameters to deterministically generate its address.

{% hint style="info" %}
💡 Value in hexadecimal : e.g.`0x24`&#x20;
{% endhint %}

***

## Vault Deployment Result

When the `createVaultProxy` transaction is executed, it results in the deployment of **three smart contracts**:

* **Transparent upgradeable proxy**\
  This is the main contract representing your Vault. It manages deposits, withdrawals, valuations, settlements, and fees. It is also the ERC20 address of the recept token representing a share of the vault.
* **Silo Contract**\
  This contract is where pending deposits and withdrawals tokens (underlying and shares) are stored.
*   **Proxy Admin**

    This contract is the admin of the Transparent Upgradeable Proxy. Through it, you can upgrade your vault logic. The owner of this contract is the address supplied at deployment: \_initialOwner.

**Example: Vault Deployment Output**

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-29 at 12.32.42.png" alt=""><figcaption></figcaption></figure>

> Upon successful execution, you will see 3 new contracts addresses returned—one for the Vault Proxy, one for ProxyAdmin and one for the Silo.

{% hint style="danger" %}
You must complete the Post-Deployment Operations to finalize your Vault setup.
{% endhint %}

If you meet any issue please contact us here <a href="https://t.me/LagoonSupport_bot" class="button primary" data-icon="telegram">Lagoon support</a>&#x20;
