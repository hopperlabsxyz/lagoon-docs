# Async deposit flow

### 1) User requests a deposit

```solidity
 function requestDeposit(
        uint256 assets,
        address controller,
        address owner
    ) external payable returns (uint256 requestId);Copy
```

Args:

* assets: the quantity of underlying to deposit.
* controller: the address that will control the request. The user address.
* owner: the address from which the asset will be transfered from. The user address.



{% hint style="info" %}
An asset.approve() transaction from the user to the vault should happen before the request deposit.
{% endhint %}



After the transaction, users can check their pending requests by calling:

```solidity
function pendingDepositRequest(
        uint256 requestId, 
        address controller
    ) external view returns (uint256 assets);Copy
```

Args:

* requestId, the id of the deposit request. Put 0 as a wildcard.
* controller. User address.

Return:

* Amount of assets pending



### 2) User claim his shares

After a settleDeposits, the shares are claimable and waiting in the vault.

{% hint style="warning" %}
_Claiming the shares is not mandatory to start earning._
{% endhint %}

A user can know the amount of claimable shares by calling:

```
function maxMint(address) public view virtual returns (uint256 shares)Copy
```



He can claim them by calling:

```solidity
function mint(uint256 shares, address receiver) public virtual returns (uint256)
```

or

```solidity
function deposit(uint256 assets, address receiver) public virtual returns (uint256)
```



Args:

* shares: the amount of shares to claim. Between 0 and the return of maxMint(userAddress).
* receiver: user address.



### Ux tip - Claim shares and request redeem in one tx

A user with claimable shares can directly request a redemption by calling:

```solidity
function claimSharesAndRequestRedeem(
   uint256 sharesToRedeem
    ) public onlyOpen whenNotPaused returns (uint40 requestId)Copy
```

Note: Already claimed shares can be requested to be redeemed via this method. It also means that you could only use this function to do redemption requests.

```
Example:
userClaimableShares: 10
userSharesBalance: 5
claimSharesAndRequestRedeem(15) --> valid call

userClaimableShares: 0
userSharesBalance: 5
claimSharesAndRequestRedeem(5) --> valid call
```

{% hint style="success" %}
This will improve the UX and remove one transaction in the process.
{% endhint %}

