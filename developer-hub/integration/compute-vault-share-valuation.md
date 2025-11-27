# Compute vault share valuation

_The method described below is identical to that used for an ERC4626 vault._

To calculate the value of a single share, first determine the price per share of the vault in terms of assets. To achieve this, call the function `convertToAssets(uint256 sharers) returns (uint256 assets);` with the argument set to `10 ** vault.decimals().`&#x20;

The price per share is given with a scaling factor of `10 ** underlyingAsset.decimals`. To obtain a standard value, you have to divide the price by this scaling factor.

To calculate the value of a share in dollars, multiply the price per share by the price of the underlying asset in dollars:\
`pricePerShare / 10 ** underlyingAsset.decimals * underlyingAssetPriceInDollar = shareValueInDollar`.

