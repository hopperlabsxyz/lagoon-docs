# Vaults Frontend Integration

This guide provides third-party developers with everything needed to integrate Lagoon Vaults data into their frontends.

## Data Sources Overview

Lagoon Vault data comes from three primary sources:

| Data Type                     | Source                | Access Method           |
| ----------------------------- | --------------------- | ----------------------- |
| **Vault Configuration**       | Environment Variables | Environment Variables   |
| **Real-time Blockchain Data** | Smart Contracts       | RPC Calls               |
| **Computed Metrics**          | Event Processing      | Subgraph + Calculations |

## Data sources

### 1. Get All Vaults

```http
VAULTS=[] in .env
```

**Response Structure:**

```typescript
interface Vault {
  address: string;           // Vault contract address
  chainId: number;          // Blockchain network ID
  name?: string;            // Vault display name
  logoUrl?: string;         // Vault logo URL
  shortDescription?: string; // Brief description
  description?: string;     // Full description
  averageProcessing?: string; // Settlement time
  referralShare?: string;   // Referral percentage
  assetManager?: {          // Curator information
    name: string;
    url: string;
  };
  asset?: {                 // Underlying asset
    symbol: string;
    address: string;
    decimals: number;
    chainId: number;
  };
  points?: Array<{          // Reward points
    name: string;
    value: string;
    icon: string;
  }>;
  additionalAprs?: Array<{  // Additional yield sources
    asset: string;
    estimatedRewardApr?: string;
    estimatedReward30dApr?: string;
  }>;
  visible: boolean;
}
```

### 2. Get Specific Vault

```http
Filter on chaindId and address
```

### 3. Get Vault Events (for APR calculation)

```http
Subgraph graphQL query
```

### 4. Get price feed

```http
From NEXT_PUBLIC_ASSETS
```

## Environment Configuration

### Vault Configuration

Vaults are defined in the `VAULTS` environment variable:

```json
[
  {
    "address": "0x5864672c5e37E4E58c261014F8E94Ee31f575d47",
    "chainId": 42161,
    "logoUrl": "/9summits.png",
    "shortDescription": "ETH yield strategies",
    "description": "Detailed strategy description...",
    "averageProcessing": "7 days",
    "referralShare": "15%",
    "assetManager": {
      "name": "9Summits",
      "url": "https://www.9summits.io"
    },
    "visible": true,
    "points": [
      {
        "name": "Eigenlayer Points",
        "value": "3x",
        "icon": "https://example.com/icon.png"
      }
    ]
  }
]
```

### Asset Configuration

Assets are defined in `NEXT_PUBLIC_ASSETS`:

```json
{
  "WETH": {
    "priceFeed": {
      "address": "0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419",
      "chainId": 1
    },
    "logo": "/weth.svg"
  }
}
```

### Events configuration

```graphql
query VaultEvents($first: Int!, $vaultAddress: Bytes!) {
    deposits(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      assets
      blockNumber
      blockTimestamp
      id
      owner
      sender
      shares
      transactionHash
      vault
      logIndex
    }
    withdraws(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      assets
      blockNumber
      blockTimestamp
      id
      owner
      receiver
      sender
      shares
      transactionHash
      vault
      logIndex
    }
    depositRequests(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      assets
      blockNumber
      blockTimestamp
      id
      owner
      sender
      transactionHash
      controller
      requestId
      vault
      logIndex
    }
    redeemRequests(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      blockNumber
      blockTimestamp
      id
      owner
      sender
      shares
      transactionHash
      controller
      requestId
      vault
      logIndex
    }
    settleRedeems(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      assetsWithdrawed
      blockNumber
      blockTimestamp
      epochId
      id
      settledId
      sharesBurned
      totalAssets
      totalSupply
      transactionHash
      vault
      logIndex
    }
    settleDeposits(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      assetsDeposited
      blockNumber
      blockTimestamp
      epochId
      id
      settledId
      sharesMinted
      totalSupply
      totalAssets
      transactionHash
      vault
      logIndex
    }
    totalAssetsUpdateds(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      transactionHash
      totalAssets
      id
      blockNumber
      blockTimestamp
      vault
      logIndex
    }
    newTotalAssetsUpdateds(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      transactionHash
      totalAssets
      totalSupply
      id
      blockNumber
      blockTimestamp
      vault
      logIndex
    }
    totalAssetsUpdateds(
      first: $first
      orderBy: blockTimestamp
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      transactionHash
      totalAssets
      totalSupply
      id
      blockNumber
      blockTimestamp
      logIndex
    }
    periodSummaries(
      first: $first
      orderBy: blockNumber
      orderDirection: desc
      where: { vault: $vaultAddress }
    ) {
      id
      blockTimestamp
      blockNumber
      totalAssetsAtStart
      totalSupplyAtStart
      previousPeriodNetTotalSupplyAtEnd
      previousPeriodTotalAssetsAtEnd
      previousPeriodTotalSupplyAtEnd
      vault
    }
  }
```

## Blockchain Data Access

### Key Contract Functions

#### 1. Vault Information to complete .env data

```typescript
// Get vault state
const vaultState = await publicClient.readContract({
  address: vaultAddress,
  abi: vaultAbi,
  functionName: 'totalAssets' | 'totalSupply' | 'paused'
});

// Get price per share
const pricePerShare = await publicClient.readContract({
  address: vaultAddress,
  abi: vaultAbi,
  functionName: 'convertToAssets',
  args: [parseUnits('1', vaultDecimals)]
});
```

#### 2. Asset Information for tvl calculation

```typescript
// Get asset price from Chainlink
const assetPrice = await publicClient.readContract({
  address: priceFeedAddress,
  abi: chainlinkAbi,
  functionName: 'latestRoundData'
});
```

## Data Integration Examples

### 1. Vault APR (Computed)

APR is calculated from historical vault events:

```typescript
// Fetch vault events for APR calculation from the alchemy subgraph

// APR calculation logic (simplified)
function calculateAPR(events, vault) {
  const settlements = events.settleDeposits.concat(events.settleRedeems)
    .sort((a, b) => Number(a.blockTimestamp) - Number(b.blockTimestamp));
  
  if (settlements.length < 2) return 0;
  
  const newest = settlements[settlements.length - 1];
  const oldest = settlements[0];
  
  const timeSpan = Number(newest.blockTimestamp) - Number(oldest.blockTimestamp);
  const priceChange = newest.pricePerShare / oldest.pricePerShare;
  
  // Annualized return
  const yearsElapsed = timeSpan / (365.25 * 24 * 60 * 60);
  return (Math.pow(priceChange, 1 / yearsElapsed) - 1) * 100;
}
```

### 2. TVL (Computed)

TVL is calculated from environment config

```typescript
const { totalAssets, decimals, asset } = vault;
const totalAssetFormatted = convertBigIntToNumber({
        value: BigInt(totalAssets),
        decimals: asset.decimals,
        precision: decimals,
      });

const usdPriceFormatted = convertBigIntToNumber({
        value: BigInt(asset.usdPrice.value),
        decimals: asset.usdPrice.decimals,
        precision: asset.usdPrice.precision,
      });

const vaultTvl = totalAssetFormatted * usdPriceFormatted;
```

### 3. Vault Curators (Off-chain)

```typescript
// Curator info comes from vault configuration
// From assetManager attribute in Vault env variable
```

### 4. Vault Rewards (Off-chain + External APIs)

```typescript
// Curator info comes from vault configuration
// From rewards attribute in Vault env variab
```



## Quick Reference

### Data Source Summary

| Information          | Source                          |
| -------------------- | ------------------------------- |
| **Vault Name**       | Environment Config              |
| **Vault Address**    | Environment Config              |
| **Underlying Asset** | Environment Config + Blockchain |
| **Vault APR**        | Computed from Events            |
| **Vault Curators**   | Environment Config              |
| **Vault Rewards**    | Environment Config              |
| **TVL**              | Computed from Events            |

### Key Environment Variables

* `VAULTS`: JSON array of vault configurations
* `NEXT_PUBLIC_ASSETS`: Asset definitions with price feeds
* `THEGRAPH_URLS`: GraphQL endpoints for event data

### Important Notes

1. **APR Calculation**: Requires historical settlement events and may return 0 for new vaults
2. **Asset Prices**: Sourced from Chainlink price feeds defined in asset configuration
3. **Real-time Data**: Use blockchain RPC calls for most current vault state

