# MPC (Multi-Party Computation) wallet

An **MPC (Multi-Party Computation) wallet** is a technology where a **private key is split into multiple shares** distributed among different parties. Instead of a single private key controlling an **Externally Owned Account (EOA)**, the key is generated and managed through **secure multi-party computation protocols**.

* **Threshold Signing (n/m):**
  * The wallet can require **n out of m** key shares to collaboratively sign a transaction (e.g., 2-of-3).
  * No single party ever has full access to the complete private key.
* **Off-Chain Governance (vs. Smart Contract Wallets):**
  * Unlike **smart contract wallets** (like Safe, Argent), which enforce rules (e.g., multi-sig) **on-chain**, MPC wallets handle key management **off-chain** via cryptographic protocols.
  * Governance (e.g., changing signers, threshold) is managed through the MPC protocol rather than blockchain transactions.
