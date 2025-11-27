# Vault Subgraphs

You can find the list of graphql endpoints [here](../../resources/networks-and-addresses.md).

Lagoon subgraphs include all the events emitted by vaults and some of the factories.\
On top of the vault events, you can find an extra entity named PeriodSummary:

```
type PeriodSummary @entity(immutable: false) {
  id: Bytes!
  vault: Bytes! # address
  totalAssetsAtStart: BigInt!
  totalSupplyAtStart: BigInt!
  totalAssetsAtEnd: BigInt!
  totalSupplyAtEnd: BigInt!
  netTotalSupplyAtEnd: BigInt!
  blockNumber: BigInt!
  blockTimestamp: BigInt!
  duration: BigInt!
}
```

Period summaries are not events but are piece of data that summaries key vault metrics evolution. A period being a portion of time between two updates of TotalAssets.

At the start of each period, we create a new PeriodSummary entity, meaning that as long as the vault is open, the most recent PeriodSummary will be partially filled. It will be completely filled when TotalAssets is updated again and consequently, the period is over.



Elements definition:

| Elements              | Definition                                                                                                                                          |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                  | the id of the entity.                                                                                                                               |
| `totalAssetsAtStart`  | the value of TotalAssets at the beginning of the period.                                                                                            |
| `vault`               | the address of the vault                                                                                                                            |
| `totalSupplyAtStart`  | the amount of shares at the beginning of the period.                                                                                                |
| `totalAssetsAtEnd`    | the value of TotalAssets at the end of the period, meaning after the TotalAssetsUpdate but before the potential settlements of requests.            |
| `totalSupplyAtEnd`    | the amount of shares at the end of the period, meaning after the TotalAssetsUpdate but before the settlements of requests and fee taking.           |
| `netTotalSupplyAtEnd` | the amount of shares at the end of the period, meaning after the TotalAssetsUpdate and after the fee taking but before the settlements of requests. |
| `blockNumber`         | the block number at the beginning of the period.                                                                                                    |
| `blockTimestamp`      | the timestamp at the beginning of the period.                                                                                                       |
| `duration`            | the duration of the period.                                                                                                                         |



\
<br>
