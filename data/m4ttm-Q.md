### Uppercase should only be used for constants and immutable variables
In Solidity it is convention to use lowercase for mutable variables, whereas uppercase is reserved for mutable variables and constants. There are 7 mutable variables with uppercase names.

```solidity
    uint256 MIN_TAX_RATE;
    uint256 MAX_TAX_RATE;
    uint256 DEFAULT_TAX_RATE;
    uint256 BASE_SCHNIBBLE_RATE;
    uint256 PRICE_PER_PLOT;
    int16[] REALM_BONUSES;
    uint8[] RARITY_BONUSES;
```

There are 7 occurences of this issue
<https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L12-L18>

### Use constants instead of magic numbers
```solidity
        if (munchablesStaked[mainAccount].length > 10)
```

<https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L140>

### Redundant approval check
It is redundant to check allowance before calling `transferFrom` as this will fail if the NFT has not been approved.
```solidity
        if (
            !munchNFT.isApprovedForAll(mainAccount, address(this)) &&
            munchNFT.getApproved(tokenId) != address(this)
        ) revert NotApprovedError();
```

<https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L148-L151>

### Storage key indexes are named with an enum but used to index multiple mappings
An enum `StorageKey` is used to index mappings in `ConfigStorage` but the same enum is used for several mappings, depending on their type. As the same key is used to index several mappings, the key name does not always correspond to the value.

```solidity
        MIN_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.LockManager
        );
        MAX_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.AccountManager
        );
        DEFAULT_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.ClaimManager
        );
        BASE_SCHNIBBLE_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.MigrationManager
        );
        PRICE_PER_PLOT = IConfigStorage(configStorage).getUint(
            StorageKey.NFTOverlord
        );
```

<https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L65-L79>