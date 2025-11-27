---
description: An overview of the Vault Admin role.
---

# Vault admin

This role is responsible for setting up and updating the governance structure that will manage the vault. The vault admin has significant control over the vault's key parameters and security features, defining how it operates and is governed.

It can:

* **Renounce ownership**: Removes ownership and authority over the vault.
* **Transfer ownership**: change the vault admin in a two-step process, the new admin must accept ownership before the transfer is complete.
* **Deactivate whitelist**: Disables the vault whitelist permanently.
* **Update the Valuation Oracle address.**
* **Update the Fee Receiver address.**
* **Pause/Unpause the vault**: Temporarily halts or resumes vault operations. [pause-a-vault.md](../how-to/pause-a-vault.md "mention")
* **Initiate vault closing**: Changes the vault status from "Open" to "Closing."

{% hint style="warning" %}
The Vault Admin role has full governance control, including the ability to assign and modify other roles, and should be selected with great care due to its authority.
{% endhint %}
