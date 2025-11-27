# Lagoon SDKs

The Lagoon SDKs are a collection of TypeScript libraries designed to simplify interactions with Lagoon Vaults. They offer modular, framework-agnostic core functionality that makes it easy to integrate Vault operations into any application.

Their architecture is inspired by Morpho’s SDK design principles, combining clean abstractions with specialized modules for popular web3 libraries to streamline development workflows

### Overview

The SDK consists of two main packages:

* **`@lagoon-protocol/v0-core`** - Framework-agnostic vault entities and utilities
* **`@lagoon-protocol/v0-viem`** - Viem-based extensions for seamless blockchain interactions

This library will evolve in the near future. We are actively listening to emerging integration needs and are open to discussions around external contributions.

### Features

The SDKs have a modular architecture that cleanly separates core logic from blockchain interaction layers, enabling seamless integration across diverse environments, from frontend applications to backend services.

The core package provides:

* `Token` class for ERC20 token handling

```typescript
export interface IToken {
  address: Address;
  name?: string;
  symbol?: string;
  decimals?: BigIntish;
  price?: BigIntish;
  totalSupply: bigint;
}
```

* `Vault` class for vault state management

```typescript
export interface IVault extends IToken {
  asset: Address;
  underlyingDecimals: number;
  owner: Address;
  pendingOwner: Address;
  whitelistManager: Address;
  feeReceiver: Address;
  safe: Address;
  feeRegistry: Address;
  valuationManager: Address;
  newRatesTimestamp: bigint;
  lastFeeTime: bigint;
  highWaterMark: bigint;
  cooldown: bigint;
  feeRates: Rates
  totalAssets: bigint;
  newTotalAssets: bigint;
  depositEpochId: number;
  depositSettleId: number;
  lastDepositEpochIdSettled: number;
  redeemEpochId: number;
  redeemSettleId: number;
  lastRedeemEpochIdSettled: number;
  pendingSilo: Address;
  wrappedNativeToken: Address;
  decimals: number;
  decimalsOffset: number;
  totalAssetsExpiration: bigint;
  totalAssetsLifespan: bigint;
  state: State,
  isWhitelistActivated: boolean,
  version: Version
}
```

* `VaultUtils` for common calculations
* Chain addresses and configuration constants

The Viem integration package provides:

* Blockchain data fetching capabilities
* Seamless integration with Viem clients

### Quick Start

#### Installation

Choose the package that fits your needs:

```bash
# For Viem integration (recommanded)
npm install @lagoon-protocol/v0-viem

# For framework-agnostic usage
npm install @lagoon-protocol/v0-core
```

#### Example: Calculate Vault APR

This is for example purpose only.

```typescript
import { Vault } from "@lagoon-protocol/v0-viem";
import { VaultUtils } from "@lagoon-protocol/v0-core";
import { Address, createPublicClient, http } from 'viem';
import { mainnet } from 'viem/chains';

const client = createPublicClient({
  chain: mainnet,
  transport: http(),
});

async function fetchVault(address: Address, blockNumber: bigint) {
  const vault = await Vault.fetch(address, client, { blockNumber });
  if (!vault) {
    throw new Error("Vault not found")
  }
  return vault
}

async function calculateVaultAPR({
  vaultAddress,
  startBlockNumber,
  endBlockNumber,
  decimals = 18
}: { vaultAddress: Address, startBlockNumber: bigint, endBlockNumber: bigint, decimals?: number }) {
  const startBlock = await client.getBlock({ blockNumber: startBlockNumber });
  const endBlock = await client.getBlock({ blockNumber: endBlockNumber });

  const vaultStart = await fetchVault(vaultAddress, startBlock.number);
  const vaultEnd = await fetchVault(vaultAddress, endBlock.number);

  const basePrice = vaultStart.convertToAssets(VaultUtils.ONE_SHARE);
  const currentPrice = vaultEnd.convertToAssets(VaultUtils.ONE_SHARE);

  const periodSeconds = endBlock.timestamp - startBlock.timestamp;

  const SECONDS_PER_YEAR = 365n * 24n * 60n * 60n;
  const BASIS_POINT = 10n ** BigInt(decimals + 2);

  const periodYield = ((currentPrice - basePrice) * SECONDS_PER_YEAR * BASIS_POINT);
  return periodYield / (periodSeconds * basePrice);
}

const decimals = 18
// If the vault is asynchronous, you can use the oldest and youngest TotalAssetUpdated event block numbers as the startBlockNumber and endBlockNumber, respectively.
const vaultApr = await calculateVaultAPR({ vaultAddress: '0x...', startBlockNumber: 1234n, endBlockNumber: 456n, decimals })

console.log(`vault APR: ${formatUnits(vaultApr, decimals)}%`);

```

* **GitHub Repository:** [https://github.com/hopperlabsxyz/sdk-v0](https://github.com/hopperlabsxyz/sdk-v0)
* **Core Package NPM:** [https://www.npmjs.com/package/@lagoon-protocol/v0-core](https://www.npmjs.com/package/@lagoon-protocol/v0-core)
* **Viem Package NPM:** [https://www.npmjs.com/package/@lagoon-protocol/v0-viem](https://www.npmjs.com/package/@lagoon-protocol/v0-viem)
