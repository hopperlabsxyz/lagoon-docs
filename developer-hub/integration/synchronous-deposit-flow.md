---
description: This page will guide you through activating synchronous deposits.
---

# Synchronous deposit flow

## How to do a synchronous deposit ?

Synchronous deposits can be done by calling:

```solidity
function syncDeposit(uint256 assets, address receiver, address referral) payable returns (uint256 shares);
```

| Variable | Definition                                                                |
| -------- | ------------------------------------------------------------------------- |
| assets   | Amount of assets to deposit.                                              |
| receiver | Recipient of the shares.                                                  |
| referral | Address that referred the depositor. Put the address 0 if not applicable. |



## How to know if synchronous deposits are currently possible?

if the function `isTotalAssetsValid()` returns true, users can do synchronous deposits. If it returns false, users must do [async deposits](async-deposit-flow.md).

```solidity
function isTotalAssetsValid() returns (bool);
```





## Typescript example with viem

Here is an example of how to decide weither a user should do a synchronous or an asynchronous deposit.

```typescript
const isSyncDepositAllowed = await client.readContract({
  abi: vaultAbi,
  address: vault.address,
  functionName: "isTotalAssetsValid",
});

if (isSyncDepositAllowed) {
  await client.writeContract({
    abi: vaultAbi,
    address: vault.address,
    functionName: "syncDeposit", // synchronous deposit
    args: [1000n, user.address, user.address],
  });
} else {
  await client.writeContract({
    abi: vaultAbi,
    address: vault.address,
    functionName: "requestDeposit", // asynchronous deposit
    args: [1000n, user.address, user.address],
  });
}
```



