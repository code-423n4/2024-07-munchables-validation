## [I-1] Not updating `lastPetMunchable` for renter
After each plot is farmed, the `lastPetMunchable` variable in the landlord's metadata is updated with the current timestamp. However, the `lastPetMunchable` variable in the renter's metadata is not updated.

For the renter's metadata, the `unfedSchnibbles` variable is updated as it increases with the `schnibbles` received by the renter. However, the `lastPetMunchable` variable, which aims to track the last time the renter was petted, is not updated.

```solidity
renterMetadata.unfedSchnibbles += (schnibblesTotal - schnibblesLandlord);
//@audit-info lastPetMunchable is not updated for renter

landlordMetadata.unfedSchnibbles += schnibblesLandlord;
landlordMetadata.lastPetMunchable = uint32(timestamp);
```

## [I-2]Missing check for Rarity and Realm for invalid values

The enum structures `Rarity` and `Realm` have an `Invalid` value that indicates the attributes of tokens are not set correctly.

There should be an additional check for `Rarity` and `Realm` to ensure they are not invalid. When calculating the index for the `REALM_BONUS` and `RARITY_BONUSES`, the indices of the arrays `REALM_BONUSES[]` and `RARITY_BONUSES[]` are directly calculated without checking if any of the realm or rarity values might be invalid.


## [I-3] Improper order of the modifiers
The `notPaused` modifier should be placed before the `forceFarmPlots` modifier. Modifiers in Solidity are executed one by one in the order they are placed. It is more appropriate to first check if the contract is paused, and then execute the logic for farming the plots.