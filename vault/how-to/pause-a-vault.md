# Pause a vault

## Overview

The `pause()` function halts all core operations of the vault and can only be executed by the vault `admin`. This is a critical administrative function used for emergency situations or maintenance periods.

## Function signature

```solidity
function pause() public onlyOwner
```

## Operations affected

The following core operations are halted when the vault is paused:

* **All request types** - Any pending or new requests are blocked, including:
  * &#x20;`requestDeposit()`
  * `requestRedeem()`
  * `syncDeposit()`
  * `claimSharesAndRequestRedeem()`&#x20;
  * `cancelRequestDeposit()`
  * `withdraw()`
  * `redeem()`
  * `deposit()`&#x20;
  * `mint()`
* **Settlement operations** - Both deposit and redeem settlements are halted as well as closing operations, including:
  * `settleDeposit()`
  * `settleRedeem()`
  * `initiateClosing()`
  * `close()`
* **NAV proposals** - The NAV update proposal functionality is disabled, including:
  * `updateNewTotalAssets()`
* **Share transfers** - The `_update()` function is paused resulting in all transfers of shares being blocked
* **Operator operations** - The `setOperator()` function is disabled

## Usage example

```solidity
// Only the owner can call this function
vault.pause();
```

You can also call pause from etherscan or any other Ethereum client that allows contract interaction

## Events

This function emits a `Paused` event:

```solidity
event Paused(address account);
```

## Security considerations

* ⚠️ **Emergency use**: Should only be used in emergency situations or planned maintenance
* ⚠️ **User impact**: All user operations will be blocked until `unpause()` is called

## Related functions

* `unpause()` - Resumes vault operations
* `paused()` - Checks if the vault is currently paused

## Error conditions

* Reverts if called by any address other than the owner
