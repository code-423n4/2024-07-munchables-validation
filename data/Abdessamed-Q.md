# [L-01] Potential revert in Farming Plots Due to Array Index Out-of-Bounds
## Impact
The `_farmPlots` function calculates schnibbles rewards and is executed during each toiling operation. The total schnibbles depend on the calculated `finalBonus`:
```solidity
// function : _farmPlots

finalBonus = int16(REALM_BONUSES[ 
                (uint256(immutableAttributes.realm) * 5) +  uint256(landlordMetadata.snuggeryRealm) // @audit index calculation that might be > array's length
                ]) + int16(int8(RARITY_BONUSES[
                        uint256(immutableAttributes.rarity)
                    ]));
schnibblesTotal = (timestamp - _toiler.lastToilDate) * BASE_SCHNIBBLE_RATE;
schnibblesTotal = uint256((int256(schnibblesTotal) + (int256(schnibblesTotal) * finalBonus)) / 100);
```

The `finalBonus` value depends on the calculated indexes to access `REALM_BONUSES` and `RARITY_BONUSES` arrays. However, there is no verification to ensure that the calculated index does not exceed the array lengths. If the index exceeds the array length, it will result in a transaction revert due to an out-of-bounds runtime error. This issue is particularly likely to occur because the  `REALM_BONUSES` and `RARITY_BONUSES` arrays can change frequently through the [configUpdated](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L88-L90)

## Recommended Mitigation
Consider verifying the calculated index if it exceeds the array's length or not.


# [L-02] `lastPetMunchable` is not updated on `_farmPlots`
## Impact
The `lastPetMunchable` field in the `MunchablesCommonLib.Player` struct represents the last timestamp when a munchable was petted by the player. This timestamp should be updated whenever the player interacts with a munchable. The issue is that this field is not updated in `_farmPlots` for the player, like it is updated for the landlord:
```solidity
function _farmPlots(address _sender) internal {

    // ...
    renterMetadata.unfedSchnibbles += (schnibblesTotal - schnibblesLandlord);
    // @audit renterMetadata.lastPetMunchable is not updated
    
    landlordMetadata.unfedSchnibbles += schnibblesLandlord;
    landlordMetadata.lastPetMunchable = uint32(timestamp);
    accountManager.updatePlayer(landlord, landlordMetadata);
    
    emit FarmedSchnibbles(
         _toiler.landlord,
        tokenId,
        _toiler.plotId,
        schnibblesTotal - schnibblesLandlord,
        schnibblesLandlord
    );


    accountManager.updatePlayer(mainAccount, renterMetadata);
}
```
## Recommended Mitigation
Consider updating the `lastPetMunchable` in the player's metadata:
```diff
+ renterMetadata.lastPetMunchable = uint32(timestamp);
```

# [L-03] `initialize` function can be front-run
## Impact
The `LandManager` contract is upgradeable, meaning the initialize function must be called to initialize the contract. The issue is that this function can be front-run upon deploying the implementation contract, allowing the front-runner to set their own config storage configurations (`_configStorage`), forcing the owner to redeploy the implementation and initialize it again.

## Recommended Mitigation
Consider initializing the contract in the same transaction of the deployment, or restrict the function access to the owner.