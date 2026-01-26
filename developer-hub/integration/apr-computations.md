---
description: This page describes the various methods used to compute Lagoon Vaults APR.
---

# APR computations

In order to compute those metrics, we will rely on the Period Summaries given by the [Lagoon subgraphs.](vault-subgraphs.md)&#x20;

{% hint style="info" %}
Period summaries are pieces of data that summaries key vault metrics. A period being the time between two updates of TotalAssets.
{% endhint %}

As a reminder, here is what we can find inside a Period Summary:

```graphql
type PeriodSummary @entity(immutable: false) {
  id: Bytes!
  vault: Bytes! # address
  totalAssetsAtStart: BigInt!
  totalSupplyAtStart: BigInt!
  totalAssetsAtEnd: BigInt!
  totalSupplyAtEnd: BigInt! # Before fee taking
  netTotalSupplyAtEnd: BigInt! # After fee taking
  blockNumber: BigInt! 
  blockTimestamp: BigInt! # Timestamp of the start of the period
  duration: BigInt! # Duration of the period
}
```



## Single Period APR

### How to compute the linear Net APR of a single period ?

Using totalAssetsAtStart and totalSupplyAtStart we can get the pricePerShareAtStart:

```typescript
import { VaultUtils } from '@lagoon-protocol/v0-core';

const ppsAtStart = VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
    decimalsOffset: vaultDecimals - assetDecimals,
    totalAssets: summary.totalAssetsAtStart,
    totalSupply: summary.totalSupplyAtStart,
  });
```

Then using totalAssetsAtEnd and netTotalSupplyAtEnd we can get the netPricePerShareAtEnd:

```typescript
const netPPSAtEnd = VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
    decimalsOffset: vaultDecimals - assetDecimals,
    totalAssets: summary.totalAssetsAtEnd,
    totalSupply: summary.netTotalSupplyAtEnd,
  });
```



Using those 2 values, we can compute the evolution and annualize it using the period duration.

```typescript
const gain = netPPSAtEnd - ppsAtStart;
const decimals = 18;
const SECONDS_PER_YEAR = 365n * 24n * 60n * 60n;

# used to increase the precision of the result
const INCREASE_PRECISION = 10n ** BigInt(decimals + 2);


const periodYield = gain * SECONDS_PER_YEAR * INCREASE_PRECISION;
return Number(
    formatUnits(periodYield / (summary.duration * oldestPrice), decimals)
);
```



### How to compute the linear Gross APR of a single period ?

The method is exactly the same as the one used previously. The difference will reside in the totalSupply used at the end of the period. This time we use summary.totalSupplyAtEnd.

```typescript
const netPPSAtEnd = VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
    decimalsOffset: vaultDecimals - assetDecimals,
    totalAssets: summary.totalAssetsAtEnd,
    totalSupply: summary.totalSupplyAtEnd,
 });
```





## Multiple periods APR

### How to select a subset of periods summaries for a given duration, eg. 30 days?

1\) Find the most recent period summary within the timeframe, here 30 days. If none is found, APR is 0.

2\) Determine the start of the period by removing the duration to the end of the most recent period summary. We will call this value the `targetTimestamp`.

3\) Find the price per share at `targetTimestamp:`

* if `targetTimestamp` is older than the first periodSummary, we will use the first periodSummary timestamp and pps
* if the `targetTimestamp` is within a periodSummary `p`, we will do an linear interpolation of the price per share using the `p.startPricePerShare,` and `p.endNetPricePerShare` .



Here are a code snippet and a visual representation of the computation process:

```typescript
export function computeInterpolatedApr(
  timeFrame: number,
  {
    periodSummaries,
    vaultDecimals,
    assetDecimals,
   
  }: {
    periodSummaries: Transaction[];
    vaultDecimals: number;
    assetDecimals: number;
  }
) {
  
    if (periodSummaries.length === 0) {
      return 0;
    }
    const vaultAddress = periodSummaries[0].data.vaultAddress;
    const chainId = periodSummaries[0].chainId;

    // first we want to check if the most recent period summary is within the timeframe
    const { newest } = extractOldestAndNewestEvent(periodSummaries)!;
    assertPeriodSummary(newest);

    if (
      isTimestampOlderThan(
        Number(newest.timestamp) + Number(newest.data.duration),
        timeFrame
      )
    ) {
      return 0;
    }

    const extrapolationTargetTimestamp = Number(newest.timestamp) - timeFrame;

    const priceReference = getPriceReferenceAtTimestamp({
      periodSummaries,
      timestamp: extrapolationTargetTimestamp,
      vaultDecimals,
      assetDecimals,
    });
    if (!priceReference) {
      return 0;
    }
    const startPrice = priceReference.pricePerShare;
    const startTimestamp = priceReference.timestamp;

    const newPrice = VaultUtils.convertToAssets(
      10n ** BigInt(vaultDecimals),
      {
        decimalsOffset: vaultDecimals - assetDecimals,
        totalAssets: newest.data.totalAssetsAtStart,
        totalSupply: newest.data.totalSupplyAtStart,
      }
    );

  const netApr = computeAPR({
     newPrice,
     newTimestamp: newest.timestamp,
     oldPrice: startPrice,
     oldTimestamp: startTimestamp,
   });
}

/**
 * @description Get the most recent and oldest events of an array of sorted events.
 * @param events - The array of events to get the most recent and oldest events from.
 * @returns The most recent and oldest events.
 */
export function extractOldestAndNewestEvent<T extends { timestamp: BigIntish }>(
  events: T[]
):
  | {
      oldest: T;
      newest: T;
    }
  | undefined {
  if (events.length == 0) {
    return undefined;
  } else {
    const isAscending =
      events[0].timestamp < events[events.length - 1].timestamp;
    return {
      oldest: isAscending ? events[0] : events[events.length - 1],
      newest: isAscending ? events[events.length - 1] : events[0],
    };
  }
}

export function isTimestampBeforeDuration(
  timestamp: number,
  duration: number
): boolean {
  return timestamp < new Date().getTime() / 1000 - duration;
}

/**
 * Get the price per share at a timestamp, using an interpolation between inside a period summary. If the target timestamp is too old, we return the closest price per share.
 *  @param periodSummariesSubset - The period summaries to get a subset of, expected to be sorted by blockTimestamp.
 * @param timestamp - The timestamp to get the price per share at.
 * @param vaultDecimals - The number of decimals the vault has.
 * @param assetDecimals - The number of decimals the asset has.
 * @returns The price per share at the timestamp and the final timestamp selected.
 */
export function getPriceReferenceAtTimestamp({
  periodSummaries,
  timestamp,
  vaultDecimals,
  assetDecimals,
}: {
  periodSummaries: Transaction[];
  timestamp: number;
  vaultDecimals: number;
  assetDecimals: number;
}):
  | {
      pricePerShare: bigint;
      timestamp: BigIntish;
    }
  | undefined {
  const decimalsOffset = vaultDecimals - assetDecimals;
  const computePps = (totalAssets: bigint, totalSupply: bigint) =>
    VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
      decimalsOffset,
      totalAssets,
      totalSupply,
    });
  if (periodSummaries.length === 0) {
    throw new Error('GetPricePerShareAtTimestamp: periodSummaries is empty');
  }
  const isAscending =
    periodSummaries[0].timestamp <
    periodSummaries[periodSummaries.length - 1].timestamp;

  // we want the period summaries to be in descending order
  if (isAscending) {
    // make a deep copy of the array
    const reversed: Transaction[] = [...periodSummaries];
    reversed.reverse();
    periodSummaries = reversed;
  }

  // earliest summary older than timestamp
  let summary = periodSummaries.find((p) => Number(p.timestamp) <= timestamp);
  if (!summary) {
    // if no summary before timestamp, we use the oldest summary
    // usually the pps will be 1 but we might handle vault migrations with a native
    // pps different from 1
    summary = periodSummaries[periodSummaries.length - 1];

    const pricePerShare = computePps(
      summary.data.totalAssetsAtStart,
      summary.data.totalSupplyAtStart
    );
    return {
      pricePerShare,
      timestamp: Number(summary.timestamp),
    };
  }
 
  if (Number(summary.timestamp) + Number(summary.data.duration) < timestamp) {
    throw new Error(
      `GetPricePerShareAtTimestamp: summary before timestamp doesn't end after timestamp. Period summary: ${summary.timestamp} + ${summary.data.duration} < ${timestamp}`
    );
  } // this should not happen

  const atStartPps = computePps(
    summary.data.totalAssetsAtStart,
    summary.data.totalSupplyAtStart
  );

  const atEndPps = computePps(
    summary.data.totalAssetsAtEnd,
    summary.data.netTotalSupplyAtEnd
  );

  const ppsEvolutionDuringPeriod = atEndPps - atStartPps;

  // timepast between start of summary and timestamp
  const timePast = timestamp - Number(summary.timestamp);

  // we do an interpolation between the start and end price per share of the summary
  const ppsEvolution =
    (ppsEvolutionDuringPeriod * BigInt(timePast)) /
    BigInt(summary.data.duration);

  return {
    pricePerShare: atStartPps + ppsEvolution,
    timestamp,
  };
}

```

##

<figure><img src="../../.gitbook/assets/APR computation 2.png" alt=""><figcaption></figcaption></figure>

## How to compute a linear 30day Gross APR ?

It is not possible to do this, as it would only remove the fees for the last period used, which is not an accurate indicator of what the yield would have been without fees over multiple periods.



## How to compute a Time-Weighted Rate Return (TWRR) 30day ?

The [summaries subset selection](apr-computations.md#how-to-select-a-subset-of-periods-summaries-for-a-given-duration-eg.-30-days) will be the same as previously described.

In this computation we will do the average of the linear APR of the various periods, weighted by their duration.

Here is an example:

| Period | Duration | APR |
| ------ | -------- | --- |
| 1      | 400      | 10% |
| 2      | 600      | 20% |

TotalDuration = 400 + 600 = 1000.

Period 1 Time Weighted APR (P1TW) : 400 / 1000 \* 10% = 0.4 \* 10% = 4%.

Period 2 Time Weighted APR (P2TW) : 600 / 1000 \* 20% = 0.6 \* 20% = 12%.

TWRR : P1TW + P2TW = 16%.



Here is a typescript example to compute the gross TWRR over a subset of Period Summaries.&#x20;

{% hint style="info" %}
We compute the Gross TWRR because we use summary.totalSupplyAtEnd. If we use summary.netTotalSupplyAtEnd we will get the Net TWRR.
{% endhint %}

```typescript
function computeTWRR(
    summaries: PeriodSummaryEvent[],
    decimals: number,
  ): number
     {
    let grossTWR = 0;
    let totalDuration = 0;

    for (let i = 0; i < summaries.length; i++) {
      const summary = summaries[i];

      const ppsAtStart = VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
        decimalsOffset: vaultDecimals - assetDecimals,
        totalAssets: summary.totalAssetsAtStart,
        totalSupply: summary.totalSupplyAtStart,
      });
      
      const grossPPSAtEnd = VaultUtils.convertToAssets(10n ** BigInt(vaultDecimals), {
        decimalsOffset: vaultDecimals - assetDecimals,
        totalAssets: summary.totalAssetsAtEnd,
        totalSupply: summary.totalSupplyAtEnd,
      });
      

      const duration = Number(currentPeriod.duration);

      const grossApr = this.computeApr({
        startPricePerShare: pricePerShareAtStart,
        endPricePerShare: grossPricePerShareAtEnd,
        duration,
      });

      grossTWR += grossApr * duration;
      totalDuration += duration;
    }
    return grossTWR: grossTWR / totalDuration;
  }
```

