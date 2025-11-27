# Get a user position

Lagoon vaults are based on [ERC7540](https://eips.ethereum.org/EIPS/eip-7540) ; the standard for asynchronous deposits and redemptions in a vault.

7540 is an extension of 4626 that add functions around requests and tweak others like `max` functions and deposit/mint/withdraw/redeem functions.

A user total share balance is the addition of the return of three functions:

* `vault.balanceOf(userAddress);` → ERC20 function
* `vault.maxMint(userAddress);` → shares ready to be claimed (to not be confused with the ERC4626 maxMint function)
* `vault.pendingRedeemRequest(0, userAddress);` → user shares waiting to be redeemed. [Why 0 ?](https://eips.ethereum.org/EIPS/eip-7540#request-ids)

