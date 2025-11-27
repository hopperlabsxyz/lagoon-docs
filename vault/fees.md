---
description: Set up your fee system
---

# Fees

## **Management Fees**

Management fees are a periodic charge on the assets under management (AUM) to compensate for managing the vault. They accumulate over time and are collected during the vault's settlement, based on the updated valuation.

$$
\text{managementFee} = \left( \frac{\text{assets} \times \text{rate}}{\text{BPS}} \right) \times \frac{\text{timeElapsed}}{\text{1 year}}
$$

* **assets** is the total assets being managed.
* **rate** is the fee rate for 1 year in BPS.
* **BPS** is a constant representing "Basis Points", 10 000 for percentage conversions (e.g.,  1% = 100 BPS).
* **timeElapsed** is the time period for which the fee is calculated.

{% hint style="info" %}
The management fee increases linearly over time based on the assets curated and the management rate.
{% endhint %}

## **Performance Fees**

Performance fees are applied exclusively to profits and are calculated only when the asset value surpasses the **high-water mark** (the highest reached price per share). This ensures fees are charged solely on new profits, not on recovered losses.

$$
\begin{align*}
\text{If } \text{pricePerShare} > \text{highWaterMark}: \\
\text{profit} &= (\text{pricePerShare} - \text{highWaterMark}) \times \text{totalSupply} \\[1em]
\text{performanceFee} &= \frac{\text{profit} \times \text{rate}}{\text{BPS}}
\end{align*}
$$

* **pricePerShare** is the current price per share.
* **highWaterMark** is the highest price per share reached.
* **totalSupply** is the total number of shares.
* **rate** is the performance fee rate (in basis points).
* **BPS** is a constant representing basis points (10 000).

{% hint style="info" %}
The performance fee is directly tied to profits and managers are rewarded only for returns exceeding the high-water mark.
{% endhint %}

## Protocol fees

Lagoon earns a percentage of the fees collected by the vault. For example, if the vault accumulates 100 shares each from performance and management fees, and the protocol fee rate is 10%, Lagoon receives 10% of the combined 200 shares, equating to 20 shares.

## Maximum Fee Limits

**Maximum fees**: Limits are set to protect depositors from excessive fees:

* Maximum Management Fees: 10%
* Maximum Performance Fees: 50%
* Maximum Protocol Fees: 30%. Lagoon charges 10% fees.

## Cooldown period

Fee percentage changes are subject to an immutable arbitrary cooldown period (e.g. 30 days). This prevents sneaky updates that could impact depositors trust.

## **Fee Distribution**

Fees are distributed to the `feeReceiver` address at every settlement in the form of shares.
