# Upgradability & Opt-in Proxy

Lagoon vaults are [Transparent Upgradable Proxy](https://rareskills.io/post/transparent-upgradeable-proxy). This type of contract allow there administrator to upgrade the logic used.

Lagoon uses edited versions of the [OpenZeppelin Transparent Upgradeable Proxy](https://docs.openzeppelin.com/contracts/5.x/api/proxy#TransparentUpgradeableProxy) and [ProxyAdmin](https://docs.openzeppelin.com/contracts/5.x/api/proxy#ProxyAdmin), allowing them to carry two more features: an Opt-in system and a Delay mechanism.



## Opt-in system

Vault administrator can upgrade their vault to a limited set of audited versions, developed and whitelisted by Lagoon.

When a new version is available, the vault admin can choose to upgrade at any time. Rolling back to a previous version is also supported.

## Delay mechanism

Every upgrade will take at least 24 hours and at most 30 days to be effective. This system ensures that users have time to evaluate the in-coming upgrade and request a redemption if they want to.

This delay itself can be upgraded. This change is also subject to the same delay mechanism.





## Design&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-08-04 at 15.28.46.png" alt=""><figcaption></figcaption></figure>



### Opt-in Proxy

The Opt-in Proxy is what you will usually consider as the vault. This contract is a TransparentUpgradbleProxy by OpenZeppelin with two differences. First it doesn't deploy a ProxyAdmin but DelayProxyAdmin. Second, before each implementation update, it will asked the authorization to the ProtocolRegistry, by calling `canUseLogic(address oldLogic, address newLogic).`&#x20;



### ProtocolRegistry

A protocol level smart-contract that contains a whitelist of authorized implementations.

| Function                                                            | Description                                                                                            |
| ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `canUseLogic(address oldLogic, address newLogic) returns (boolean)` | This function checks if newLogic is an authorized Lagoon vault version, in this case, it returns true. |



### DelayProxyAdmin

A Proxy Admin is a smart-contract deployed by an Opt-inProxy at its creation. This contract is the only address that can upgrade the Opt-inProxy.

The DelayProxyAdmin has an editable owner that is the only one capable of doing the following calls:

| Function                                                                                                                                                  | Description                                                                                                                      |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `submitImplementation(address _implementation) onlyOwner`                                                                                                 | Submit an address in the DelayProxyAdmin contract. When the delay is passed, the owner can upgrade the contract to this address. |
| <p><code>upgradeAndCall(</code><br><code>address proxy,</code><br><code>address,</code><br><code>bytes memory data</code><br><code>) onlyOwner</code></p> | Upgrade the target proxy using the implementation previously stored via submitImplementation.                                    |
| <p><code>submitDelay(</code><br><code>uint256 _delay</code><br><code>) onlyOwner</code></p>                                                               | Submit a new delay. When the current delay is passed, the new delay can be applied.                                              |
| `function updateDelay() onlyOwner`                                                                                                                        | Update the value of the delay to be the one previously submitted via submitDelay.                                                |
| `renounceOwnership() onlyOwner`                                                                                                                           | Renounce the ownership of the DelayAdminProxy making it unusable.                                                                |
| `function transferOwnership(address newOwner)`                                                                                                            | Transfer the ownership and thus the vault upgradability management to another address.                                           |





### Q\&A

<details>

<summary>Can I make my vault immutable ?</summary>

Yes, a vault can become immutable if the owner of DelayProxyAdmin gives up his ownership.

</details>

<details>

<summary>Can I rollback a vault upgrade ?</summary>

Any form of downgrades are authorized for now. Note that a downgrade won't reinitialize the state of your vault, which means that if you downgrade to the previous version, you will get back the previous state.

</details>

<details>

<summary>How can I be informed of new vault versions releases ?</summary>

Stay updated on Lagoon developments [here](https://t.me/+mvB7YsN80P5jNTVk).

</details>

<details>

<summary>Are the vault owner (admin) and the DelayProxyAdmin owner the same ?</summary>

No, those are two different variables present in two different contracts.\
The vault owner, that we usually call the admin if defined [here](roles-and-capacities/vault-admin.md). His scope is exclusively over the vault roles and operations. On the other hand, the owner of the DelayProxyAdmin can only do operations related to the upgradability of the proxy.\
They can have the same value but changing one won't change the other.

</details>
