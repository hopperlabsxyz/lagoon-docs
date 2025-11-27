# Close a vault

### Overview

Closing a vault permanently shuts down its operations and allows users to withdraw their assets at a fixed price. The process requires coordination between the vault admin and the safe contract.

### 3-Step Closure Process

#### Step 1: Initiate Closure (Admin)

The vault admin starts the closure process:

```solidity
vault.initiateClosing();
```

**What happens:**

* Vault state changes to `Closing`
* New deposit settlements are blocked
* Users can still request deposits but they won't be processed

#### Step 2: Update NAV (Valuation Oracle)

Update the vault's value before final closure:

<pre class="language-solidity"><code class="lang-solidity"><strong>vault.updateNewTotalAssets(1000000); // Current market value
</strong></code></pre>

#### Step 3: Complete Closure (Safe)

The safe contract finalizes the closure:

```solidity
vault.close(1000000); // Must match the NAV from step 2
```

**What happens:**

* Updates final asset value
* Settles all pending requests
* Takes final fees
* Transfers all assets to vault contract
* Vault state changes to `Closed`
* Share price becomes fixed

### After Closure

Users can only:

* Withdraw their assets using `withdraw()` or `redeem()`
* Claim any pending settled requests
* Transfer shares

### Important Notes

⚠️ **Permanent action** - Cannot be reversed

⚠️ **NAV matching** - The value in step 3 must match step 2

⚠️ **Asset requirements** - Safe must have enough assets for all withdrawals

### Role Requirements

| Step | Who Can Execute   | Function                 |
| ---- | ----------------- | ------------------------ |
| 1    | Vault Admin       | `initiateClosing()`      |
| 2    | Valuation Manager | `updateNewTotalAssets()` |
| 3    | Safe Contract     | `close()`                |

### Events Emitted

```solidity
event StateUpdated(State.Closing);    // Step 1
event NewTotalAssetsUpdated(uint256); // Step 2
event TotalAssetsUpdated(uint256);    // Step 3
event StateUpdated(State.Closed);     // Step 3
```
