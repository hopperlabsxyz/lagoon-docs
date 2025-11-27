# Renounce Safe & Zodiac role modifier ownership

Since your Zodiac role modifier's owner is most likely your Safe, we would advise to start renouncing the Zodiac role modifier first.

## Zodiac role modifier

The Zodiac role modifier is based on [Openzeppelin' Ownable contract](https://docs.openzeppelin.com/contracts/5.x/access-control#ownership-and-ownable). Thus you can give-up its ownership by calling `renounceOwnership`  as the owner. For now we would advise to do it on etherscan at the address of the Role Modifier.&#x20;

{% hint style="info" %}
You can easily connect as a Safe in Rabby by adding an address as an _Institutional Wallet._
{% endhint %}

## Safe

Safe doesn't natively allows to renounce ownership. It is still possible to do it by:

* putting the threshold to 1 out of m signers.
* adding a signer whose address is obviously not under your control. For easily look up and audit, we advice to use: `0xdEADBEeF00000000000000000000000000000000`
* Remove all owners except the `0xdEADBEeF` address, leading to a 1 out 1 configuration.

{% hint style="warning" %}
Renouncing ownership is effective only if external auditors can easily assess it. That's why we advise to use the `0xdEADBEeF` address.
{% endhint %}

