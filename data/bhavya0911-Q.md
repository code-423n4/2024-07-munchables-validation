## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| NC-1 | Function ordering does not follow the Solidity style guide | 1 |
| NC-2 | Natspec comments are missing | 14 |
| NC-3 | Storage mappings should be clubbed for better storage | 5 |
| NC-4 | Use `delete` keyword to remove mapping value instead of setting values to default manually | 4 |
| NC-5 | Potentially unused variables declared outside of loop  | 8 |
| NC-6 | Redundant type converting | 2 |
| NC-7 | Dividing by 1e18 for calculating percentage | 1 |
| NC-8 | Unchecked updating of same value | 1 |
### [NC-1] Contract is not structured according to Solidity Style Guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (1)*:
```solidity
File: ./src/managers/LandManager.sol

1: 
   Current order:
   public initialize
   internal _reconfigure
   external configUpdated
   external updateTaxRate
   external triggerPlotMetadata
   external updatePlotMetadata
   external stakeMunchable
   external unstakeMunchable
   external transferToUnoccupiedPlot
   external farmPlots
   internal _farmPlots
   internal _removeTokenIdFromStakedList
   internal _getMainAccountRequireRegistered
   internal _getNumPlots

   Suggested order:
   external configUpdated
   external updateTaxRate
   external triggerPlotMetadata
   external updatePlotMetadata
   external stakeMunchable
   external unstakeMunchable
   external transferToUnoccupiedPlot
   external farmPlots
   public initialize
   internal _reconfigure
   internal _farmPlots
   internal _removeTokenIdFromStakedList
   internal _getMainAccountRequireRegistered
   internal _getNumPlots

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-2] Natspec comments are missing
For all the functions, there is no natspec comments from the developer.

*Instances (14)*:
```solidity
File: ./src/managers/LandManager.sol

45:     function initialize(address _configStorage) public override initializer

50:     function _reconfigure() internal {

88:     function configUpdated() external override onlyConfigStorage {

92:     function updateTaxRate(uint256 newTaxRate) external override notPaused {

104:     function triggerPlotMetadata() external override notPaused {

116:    function updatePlotMetadata(

131:    function stakeMunchable(

173:    function unstakeMunchable(

199:    function transferToUnoccupiedPlot(

228:    function farmPlots() external override notPaused {

232:    function _farmPlots(address _sender) internal {

312:    function _removeTokenIdFromStakedList(

332:    function _getMainAccountRequireRegistered(

344:    function _getNumPlots(address _account) internal view returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-3] Storage mappings should be clubbed for better storage
Storage mappings can be clubbed together for optimising storage and retrieval.

*Instances (5)*:
```solidity
File: ./src/managers/LandManager.sol

21:    mapping(address => PlotMetadata) plotMetadata;
23:    mapping(address => mapping(uint256 => Plot)) plotOccupied;
25:    mapping(uint256 => address) munchableOwner;
27:    mapping(address => uint256[]) munchablesStaked;
29:    mapping(uint256 => ToilerState) toilerState;

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-4] Use `delete` keyword to remove mapping value instead of setting values to default manually
Instead of manually setting default values for mapping, directly use `delete` keyword to remove value of a given key in a mapping. After listing the instances of this bug, correct method of deleting in all 4 instances are shown below.

*Instances (4)*:
```solidity
File: ./src/managers/LandManager.sol

181:        plotOccupied[_toiler.landlord][_toiler.plotId] = Plot({
            occupied: false,
            tokenId: 0
        });


185:        toilerState[tokenId] = ToilerState({
            lastToilDate: 0,
            plotId: 0,
            landlord: address(0),
            latestTaxRate: 0,
            dirty: false
        });

192:        munchableOwner[tokenId] = address(0);

215:        plotOccupied[_toiler.landlord][oldPlotId] = Plot({
            occupied: false,
            tokenId: 0
        });

```
*Fixed Code (4)*
```solidity
File: ./src/managers/LandManager.sol

181:        delete plotOccupied[_toiler.landlord][_toiler.plotId];

185:        delete toilerState[tokenId];

192:        delete munchableOwner[tokenId];

215:        delete plotOccupied[_toiler.landlord][oldPlotId];

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-5] Potentially unused variables declared outside of loop
8 variables are declared outside of loop, although they are used exclusively inside loop, that also if certain condition is true. It is waste of memory resources to have unused variables that stay unused in most situations. They could have been easily declared inside the loop when required and used inside the loop itself.

*Instances (8)*:
```solidity
File: ./src/managers/LandManager.sol

239:        MunchablesCommonLib.NFTImmutableAttributes memory immutableAttributes;
240:        ToilerState memory _toiler;
241:        uint256 timestamp;
242:        address landlord;
243:        uint256 tokenId;
244:        int256 finalBonus;
245:        uint256 schnibblesTotal;
246:        uint256 schnibblesLandlord;

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-6] Redundant type converting
Conversation in mentioned instances are redundant as they are already of the type they are being converted to.

*Instances (2)*:
```solidity
File: ./src/managers/LandManager.sol

271:                int16(
                    REALM_BONUSES[

278:                    int8(RARITY_BONUSES[uint256(immutableAttributes.rarity)])

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-7] Dividing by 1e18 for calculating percentage
For converting landlord's cut, it should be divided by 100, not 1e18.

*Instances (1)*:
```solidity
File: ./src/managers/LandManager.sol

287:            schnibblesLandlord =
                (schnibblesTotal * _toiler.latestTaxRate) /
                1e18;

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)

### [NC-8] Unchecked updating of same value
In `_farmPlots()` there is a huge possibility that the rented stays the same throughout the function, so updating same value to storage will incur unnecesarry gas, which should be avoided by only storing if there's any change after running the loop.

*Instances (1)*:
```solidity
File: ./src/managers/LandManager.sol

309:         accountManager.updatePlayer(mainAccount, renterMetadata);

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol)