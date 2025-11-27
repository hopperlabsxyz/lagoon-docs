# Claim shares on behalf of users

## Overview

After deposit settlements, Vault curators can claim on behalf of users the available shares in the vault.

## 2-steps procedure

### Identify the addresses concerned

You can obtain the list of addresses with a claimable deposit request using the [find-claimable-controllers](https://github.com/hopperlabsxyz/vault-computation-cli?tab=readme-ov-file#find-claimable-controllers-fcc) command in the [vault-cli-computation](https://github.com/hopperlabsxyz/vault-computation-cli) tool.&#x20;

### Call claimSharesOnBehalf as curator

```solidity
function claimSharesOnBehalf(address[] memory controllers) external onlySafe;
```

To claim shares on behalf of others using the Curator role, execute the `claimSharesOnBehalf` function on Etherscan. Format the addresses as an array of strings like this:

```
["0x44ab...cb7e", "0x878e...b641"]
```

