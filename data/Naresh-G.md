## Summary

| |Issue|Instances| Gas Savings
|-|:-|:-:|:-:|
| [[G-01](#g-01)] | Argument validation should be at the top of the function/block | 11| 0|
| [[G-02](#g-02)] | Use `Array.unsafeAccess()` to avoid repeated array length checks | 1| 2100|
| [[G-03](#g-03)] | Constructors can be marked as `payable` to save deployment gas | 1| 21|
| [[G-04](#g-04)] | `do-while` is cheaper than `for`-loops when the initial check can be skipped | 2| 0|
| [[G-05](#g-05)] | Avoid emitting event on every iteration | 1| 375|
| [[G-06](#g-06)] | Counting down in `for` statements is more gas efficient | 2| 16|
| [[G-07](#g-07)] | Function names can be optimized | 1| 128|
| [[G-08](#g-08)] | Don't initialize variables with default value | 3| 0|
| [[G-09](#g-09)] | Optimize Deployment Size by Fine-tuning IPFS Hash | 1| 1514|
| [[G-10](#g-10)] | State variable read in a loop | 3| 291|
| [[G-11](#g-11)] | Merge events to save gas | 2| 750|
| [[G-12](#g-12)] | Multiple accesses of the same mapping/array key/index should be cached | 14| 588|
| [[G-13](#g-13)] | Multiple mappings can be replaced with a single struct mapping | 5| 210|
| [[G-14](#g-14)] | State variables should be cached in stack variables rather than re-reading them from storage | 2| 194|
| [[G-15](#g-15)] | Use Only Named Returns | 2| 88|
| [[G-16](#g-16)] | Nesting `if`-statements is cheaper than using `&&` | 1| 4|
| [[G-17](#g-17)] | Consider using OpenZeppelin's `EnumerateSet` instead of nested mappings | 1| 1000|
| [[G-18](#g-18)] | Operator `>=`/`<=` costs less gas than operator `>`/`<` | 5| 15|
| [[G-19](#g-19)] | Refactor modifiers to call a local function | 1| 1000|
| [[G-20](#g-20)] | Use assembly scratch space for building calldata for external calls | 6| 1320|
| [[G-21](#g-21)] | Use shift right/left instead of division if possible | 3| 0|
| [[G-22](#g-22)] | Usage of smaller `uint`/`int` types causes overhead | 1| 55|
| [[G-23](#g-23)] | Consider using solady's 'FixedPointMathLib' | 6| 0|
| [[G-24](#g-24)] | `address(this)` can be stored in state variable that will ultimately cost less, than every time calculating this, specifically when it used multiple times in a contract | 4| 0|
| [[G-25](#g-25)] | Stack variable cost less than state variables while used in emiting event | 2| 18|
| [[G-26](#g-26)] | Mappings are cheaper to use than storage arrays | 2| 4200|
| [[G-27](#g-27)] | Gas savings can be achieved by changing the model for assigning value to the structure | 8| 1040|
| [[G-28](#g-28)] | Optimize Storage with Byte Truncation for Time Related State Variables | 1| 2000|
| [[G-29](#g-29)] | Trade-offs Between Modifiers and Internal Functions | 5| 52500|
| [[G-30](#g-30)] | Divisions can be `unchecked` to save gas | 3| 60|
| [[G-31](#g-31)] | Using assembly to check for `address(0)` can save gas | 2| 12|
| [[G-32](#g-32)] | Use assembly to `emit` events | 8| 304|
| [[G-33](#g-33)] | Simple checks for zero can be done using assembly to save gas | 6| 6|
| [[G-34](#g-34)] | Use solady library where possible to save gas | 1| 1000|
| [[G-35](#g-35)] | Variable declared within iteration | 1| 0|
| [[G-36](#g-36)] | Enable IR-based code generation | 1| 0|
| [[G-37](#g-37)] | Using XOR (^) and AND (&) bitwise equivalents for gas optimizations | 7| 0|

Total: 123 instances over 37 issues with an estimate of 69794 gas saved.

### Gas Risk Issues

### [G-01]<a name="g-01"></a> Argument validation should be at the top of the function/block

Checks that involve function arguments should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

*There are 11 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

94:         if (newTaxRate < MIN_TAX_RATE || newTaxRate > MAX_TAX_RATE)

137:         if (landlord == mainAccount) revert CantStakeToSelfError();

138:         if (plotOccupied[landlord][plotId].occupied)

142:         if (munchNFT.ownerOf(tokenId) != mainAccount)

146:         if (plotId >= totalPlotsAvail) revert PlotTooHighError();

149:             !munchNFT.isApprovedForAll(mainAccount, address(this)) &&
150:             munchNFT.getApproved(tokenId) != address(this)

179:         if (munchableOwner[tokenId] != mainAccount) revert InvalidOwnerError();

208:         if (munchableOwner[tokenId] != mainAccount) revert InvalidOwnerError();

209:         if (plotOccupied[_toiler.landlord][plotId].occupied)

211:         if (plotId >= totalPlotsAvail) revert PlotTooHighError();

319:             if (munchablesStaked[mainAccount][i] == tokenId) {

```


*GitHub* : [94](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L94-L94), [137](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L137-L137), [138](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L138-L138), [142](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L142-L142), [146](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L146-L146), [149](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L149-L150), [179](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L179-L179), [208](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L208-L208), [209](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L209-L209), [211](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L211-L211), [319](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L319-L319)

### [G-02]<a name="g-02"></a> Use `Array.unsafeAccess()` to avoid repeated array length checks

When using storage arrays, solidity adds an internal lookup of the array's length (a Gcoldsload **2100 gas**) to ensure you don't read past the array's end. You can avoid this lookup by using [Array.unsafeAccess()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/94697be8a3f0dfcd95dfb13ffbd39b5973f5c65d/contracts/utils/Arrays.sol#L57) in cases where the length has already been checked, as is the case with the instances below

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

247:         for (uint8 i = 0; i < staked.length; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247)

### [G-03]<a name="g-03"></a> Constructors can be marked as `payable` to save deployment gas

`payable` functions cost less gas to execute, because the compiler does not have to add extra checks to ensure that no payment is provided. A constructor can be safely marked as payable, because only the deployer would be able to pass funds, and the project itself would not pass any funds.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

36:     constructor() {

```


*GitHub* : [36](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L36-L36)

### [G-04]<a name="g-04"></a> `do-while` is cheaper than `for`-loops when the initial check can be skipped

`for (uint256 i; i < len; ++i){ ... }` -> `do { ...; ++i } while (i < len);`

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

247:         for (uint8 i = 0; i < staked.length; i++) {

318:         for (uint256 i = 0; i < stakedLength; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247), [318](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L318-L318)

### [G-05]<a name="g-05"></a> Avoid emitting event on every iteration

Emitting an event has an overhead of **375 gas**, which will be incurred on every iteration of the loop. It is cheaper to `emit` only [once](https://github.com/ethereum/EIPs/blob/adad5968fd6de29902174e0cb51c8fc3dceb9ab5/EIPS/eip-1155.md?plain=1#L68) after the loop has finished.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

301:             emit FarmedSchnibbles(
302:                 _toiler.landlord,
303:                 tokenId,
304:                 _toiler.plotId,
305:                 schnibblesTotal - schnibblesLandlord,
306:                 schnibblesLandlord
307:             );

```


*GitHub* : [301](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L301-L307)

### [G-06]<a name="g-06"></a> Counting down in `for` statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable. [More info](https://solodit.xyz/issues/g-02-counting-down-in-for-statements-is-more-gas-efficient-code4rena-pooltogether-pooltogether-git)

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

247:         for (uint8 i = 0; i < staked.length; i++) {

318:         for (uint256 i = 0; i < stakedLength; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247), [318](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L318-L318)

### [G-07]<a name="g-07"></a> Function names can be optimized

Function that are `public`/`external` and public state variable names can be optimized to save gas.

Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, per sorted position shifted. [Reference](https://blog.emn178.cc/en/post/solidity-gas-optimization-function-name/)

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

11: contract LandManager is BaseBlastManagerUpgradeable, ILandManager {

```


*GitHub* : [11](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L11-L11)

### [G-08]<a name="g-08"></a> Don't initialize variables with default value



*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

247:         for (uint8 i = 0; i < staked.length; i++) {

317:         bool found = false;

318:         for (uint256 i = 0; i < stakedLength; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247), [317](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L317-L317), [318](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L318-L318)

### [G-09]<a name="g-09"></a> Optimize Deployment Size by Fine-tuning IPFS Hash

The Solidity compiler appends 53 bytes of metadata to the smart contract code, incurring an extra cost of 10,600 gas. This additional expense arises from 200 gas per bytecode, plus calldata cost, which amounts to 16 gas for non-zero bytes and 4 gas for zero bytes. This results in a maximum of 848 extra gas in calldata cost.

Reducing this cost is crucial for the following reasons:

The metadata's 53-byte addition leads to a deployment cost increase of 10,600 gas. It can also result in an additional calldata cost of up to 848 gas. Ways to Minimize Gas Consumption:

Employ the `--no-cbor-metadata` compiler option to exclude metadata. Be cautious as this might impact contract verification. Search for code comments that yield an IPFS hash with more zeros, thereby reducing calldata costs.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L1-L1)

### [G-10]<a name="g-10"></a> State variable read in a loop

The state variable should be cached in a local variable rather than reading it on every iteration of the loop, which will replace each Gwarmaccess (**100 gas**) with a much cheaper stack read.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit nftAttributesManager
267:             immutableAttributes = nftAttributesManager.getImmutableAttributes(

// @audit BASE_SCHNIBBLE_RATE
282:                 BASE_SCHNIBBLE_RATE;

// @audit accountManager
300:             accountManager.updatePlayer(landlord, landlordMetadata);

```


*GitHub* : [267](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L267-L267), [282](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L282-L282), [300](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L300-L300)

### [G-11]<a name="g-11"></a> Merge events to save gas

Consolidating multiple event emissions into a single event in Solidity can result in significant gas savings. Each event emission in Ethereum involves a gas cost, specifically for the topics logged with the event. By merging sequential events into a singular event, you can save on the Glogtopic cost, which is incurred for each topic of each event. This approach can save around 375 gas per additional topic. This strategy is particularly beneficial in functions where multiple related events are emitted in sequence. However, it's crucial to balance gas optimization with the clarity and utility of the event data for off-chain consumers

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

224:         emit FarmPlotLeave(_toiler.landlord, tokenId, oldPlotId);

225:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

```


*GitHub* : [224](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L224-L224), [225](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L225-L225)

### [G-12]<a name="g-12"></a> Multiple accesses of the same mapping/array key/index should be cached

Access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's `keccak256` hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory`/`calldata`

*There are 14 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit 'plotMetadata[landlord]' also accessed on line 96, 99
98:         uint256 oldTaxRate = plotMetadata[landlord].currentTaxRate;

// @audit 'plotMetadata[mainAccount]' also accessed on line 106
108:         plotMetadata[mainAccount] = PlotMetadata({

// @audit 'plotMetadata[landlord]' also accessed on line 119, 125
120:             plotMetadata[landlord] = PlotMetadata({

// @audit 'plotOccupied[landlord][plotId]' also accessed on line 138
154:         plotOccupied[landlord][plotId] = Plot({

// @audit 'munchablesStaked[mainAccount]' also accessed on line 140
159:         munchablesStaked[mainAccount].push(tokenId);

// @audit 'toilerState[tokenId]' also accessed on line 162
170:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

// @audit 'toilerState[tokenId]' also accessed on line 177
185:         toilerState[tokenId] = ToilerState({

// @audit 'munchableOwner[tokenId]' also accessed on line 179
192:         munchableOwner[tokenId] = address(0);

// @audit 'toilerState[tokenId]' also accessed on line 204, 225
213:         toilerState[tokenId].latestTaxRate = plotMetadata[_toiler.landlord]

// @audit 'plotOccupied[_toiler.landlord]' also accessed on line 209, 219
215:         plotOccupied[_toiler.landlord][oldPlotId] = Plot({

// @audit 'plotOccupied[_toiler.landlord][plotId]' also accessed on line 209 
219:         plotOccupied[_toiler.landlord][plotId] = Plot({

// @audit 'toilerState[tokenId]' also accessed on line 250, 291, 292
260:                 toilerState[tokenId].dirty = true;

// @audit 'munchablesStaked[mainAccount]' also accessed on line 316, 320, 324
319:             if (munchablesStaked[mainAccount][i] == tokenId) {

// @audit 'munchablesStaked[mainAccount][i]' also accessed on line 319
320:                 munchablesStaked[mainAccount][i] = munchablesStaked[

```


*GitHub* : [98](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L98-L98), [108](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L108-L108), [120](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L120-L120), [154](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L154-L154), [159](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L159-L159), [170](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L170-L170), [185](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L185-L185), [192](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L192-L192), [213](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L213-L213), [215](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L215-L215), [219](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L219-L219), [260](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L260-L260), [319](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L319-L319), [320](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L320-L320)

### [G-13]<a name="g-13"></a> Multiple mappings can be replaced with a single struct mapping

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

21:     mapping(address => PlotMetadata) plotMetadata;

23:     mapping(address => mapping(uint256 => Plot)) plotOccupied;

25:     mapping(uint256 => address) munchableOwner;

27:     mapping(address => uint256[]) munchablesStaked;

29:     mapping(uint256 => ToilerState) toilerState;

```


*GitHub* : [21](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L21-L21), [23](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L23-L23), [25](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L25-L25), [27](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L27-L27), [29](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L29-L29)

### [G-14]<a name="g-14"></a> State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the **second+** access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit 'munchNFT' also accessed on line 142, 150, 152
149:             !munchNFT.isApprovedForAll(mainAccount, address(this)) &&

// @audit 'accountManager' also accessed on line 300
309:         accountManager.updatePlayer(mainAccount, renterMetadata);

```


*GitHub* : [149](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L149-L149), [309](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L309-L309)

### [G-15]<a name="g-15"></a> Use Only Named Returns

The Solidity compiler can generate more efficient bytecode when using named returns. It's recommended to replace anonymous returns with named returns for potential gas savings.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

332:     function _getMainAccountRequireRegistered(
333:         address _account
334:     ) internal view returns (address, MunchablesCommonLib.Player memory) {

344:     function _getNumPlots(address _account) internal view returns (uint256) {

```


*GitHub* : [332](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L332-L334), [344](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L344-L344)

### [G-16]<a name="g-16"></a> Nesting `if`-statements is cheaper than using `&&`

Using a double if statement instead of logical AND (&&) can provide similar short-circuiting behavior whereas double if is slightly more efficient.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

148:         if (
149:             !munchNFT.isApprovedForAll(mainAccount, address(this)) &&
150:             munchNFT.getApproved(tokenId) != address(this)

```


*GitHub* : [148](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L148-L150)

### [G-17]<a name="g-17"></a> Consider using OpenZeppelin's `EnumerateSet` instead of nested mappings

Nested mappings and multi-dimensional arrays in Solidity operate through a process of double hashing, wherein the original storage slot and the first key are concatenated and hashed, and then this hash is again concatenated with the second key and hashed. This process can be quite gas expensive due to the double-hashing operation and subsequent storage operation (sstore).
OpenZeppelin's `EnumerableSet` provides a potential solution to this problem. It creates a data structure that combines the benefits of set operations with the ability to enumerate stored elements, which is not natively available in Solidity. EnumerableSet handles the element uniqueness internally and can therefore provide a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays in certain scenarios.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

23:     mapping(address => mapping(uint256 => Plot)) plotOccupied;

```


*GitHub* : [23](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L23-L23)

### [G-18]<a name="g-18"></a> Operator `>=`/`<=` costs less gas than operator `>`/`<`

The compiler uses opcodes `GT` and `ISZERO` for code that uses `>`, but only requires `LT` for `>=`. A similar behavior applies for `>`, which uses opcodes `LT` and `ISZERO`, but only requires `GT` for `<=`. It can [save 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde) for each. It should be converted to the `<=`/`>=` equivalent when comparing against integer literals.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

94:         if (newTaxRate < MIN_TAX_RATE || newTaxRate > MAX_TAX_RATE)

140:         if (munchablesStaked[mainAccount].length > 10)

247:         for (uint8 i = 0; i < staked.length; i++) {

258:             if (_getNumPlots(landlord) < _toiler.plotId) {

318:         for (uint256 i = 0; i < stakedLength; i++) {

```


*GitHub* : [94](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L94-L94), [140](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L140-L140), [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247), [258](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L258-L258), [318](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L318-L318)

### [G-19]<a name="g-19"></a> Refactor modifiers to call a local function

Modifiers code is copied in all instances where it's used, increasing bytecode size. By doing a refractor to the internal function, one can reduce bytecode size significantly at the cost of one JUMP.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

40:     modifier forceFarmPlots(address _account) {
41:         _farmPlots(_account);
42:         _;
43:     }

```


*GitHub* : [40](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L40-L43)

### [G-20]<a name="g-20"></a> Use assembly scratch space for building calldata for external calls

If an external call's calldata can fit into two or fewer words, use the scratch space to build the calldata, rather than allowing Solidity to do a memory expansion.

*There are 6 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

46:         BaseBlastManagerUpgradeable.initialize(_configStorage);

152:         munchNFT.transferFrom(mainAccount, address(this), tokenId);

195:         munchNFT.transferFrom(address(this), mainAccount, tokenId);

300:             accountManager.updatePlayer(landlord, landlordMetadata);

309:         accountManager.updatePlayer(mainAccount, renterMetadata);

335:         (
336:             address _mainAccount,
337:             MunchablesCommonLib.Player memory _player
338:         ) = accountManager.getPlayer(_account);

```


*GitHub* : [46](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L46-L46), [152](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L152-L152), [195](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L195-L195), [300](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L300-L300), [309](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L309-L309), [335](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L335-L338)

### [G-21]<a name="g-21"></a> Use shift right/left instead of division if possible

While the `DIV` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

284:                 (int256(schnibblesTotal) +
285:                     (int256(schnibblesTotal) * finalBonus)) / 100

288:                 (schnibblesTotal * _toiler.latestTaxRate) /
289:                 1e18;

345:         return lockManager.getLockedWeightedValue(_account) / PRICE_PER_PLOT;

```


*GitHub* : [284](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L284-L285), [288](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L288-L289), [345](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L345-L345)

### [G-22]<a name="g-22"></a> Usage of smaller `uint`/`int` types causes overhead

When using a smaller int/uint type it first needs to be converted to it's 258 bit counterpart to be operated, this increases the gass cost and thus should be avoided. However it does make sense to use smaller int/uint values within structs provided you pack the struct properly.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

247:         for (uint8 i = 0; i < staked.length; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247-L247)

### [G-23]<a name="g-23"></a> Consider using solady's 'FixedPointMathLib'

Using Solady's 'FixedPointMathLib' for multiplication or division operations in Solidity can lead to significant gas savings. This library is designed to optimize fixed-point arithmetic operations, which are common in financial calculations involving tokens or currencies. By implementing more efficient algorithms and assembly optimizations, 'FixedPointMathLib' minimizes the computational resources required for these operations. For contracts that frequently perform such calculations, integrating this library can reduce transaction costs, thereby enhancing overall performance and cost-effectiveness. However, developers must ensure compatibility with their existing codebase and thoroughly test for accuracy and expected behavior to avoid any unintended consequences.

*There are 6 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

273:                         (uint256(immutableAttributes.realm) * 5) +

281:                 (timestamp - _toiler.lastToilDate) *
282:                 BASE_SCHNIBBLE_RATE;

284:                 (int256(schnibblesTotal) +
285:                     (int256(schnibblesTotal) * finalBonus)) / 100

288:                 (schnibblesTotal * _toiler.latestTaxRate) /
289:                 1e18;

345:         return lockManager.getLockedWeightedValue(_account) / PRICE_PER_PLOT;

```


*GitHub* : [273](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L273-L273), [281](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L281-L282), [284](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L284-L285), [285](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L285-L285), [288](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L288-L289), [345](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L345-L345)

### [G-24]<a name="g-24"></a> `address(this)` can be stored in state variable that will ultimately cost less, than every time calculating this, specifically when it used multiple times in a contract



*There are 4 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

149:             !munchNFT.isApprovedForAll(mainAccount, address(this)) &&

150:             munchNFT.getApproved(tokenId) != address(this)

152:         munchNFT.transferFrom(mainAccount, address(this), tokenId);

195:         munchNFT.transferFrom(address(this), mainAccount, tokenId);

```


*GitHub* : [149](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L149-L149), [150](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L150-L150), [152](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L152-L152), [195](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L195-L195)

### [G-25]<a name="g-25"></a> Stack variable cost less than state variables while used in emiting event

When emitting events in Solidity, using stack variables (local variables within a function) instead of state variables can lead to significant gas savings. Stack variables reside in memory only for the duration of the function execution and are less costly to access compared to state variables, which are stored on the blockchain. When an event is emitted, accessing these stack variables requires less gas than fetching data from state variables, which involves reading from the contract's storage - a more expensive operation. Thus, for efficiency, prefer using local variables within functions for event emission, especially in functions that are called frequently.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit  toilerState
170:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

// @audit  toilerState
225:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

```


*GitHub* : [170](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L170-L170), [225](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L225-L225)

### [G-26]<a name="g-26"></a> Mappings are cheaper to use than storage arrays

When using storage arrays, solidity adds an internal lookup of the array's length (a Gcoldsload **2100 gas**) to ensure you don't read past the array's end. You can avoid this lookup by using a `mapping` and storing the number of entries in a separate storage variable. In cases where you have sentinel values (e.g. 'zero' means invalid), you can avoid length checks

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

17:     int16[] REALM_BONUSES;

18:     uint8[] RARITY_BONUSES;

```


*GitHub* : [17](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L17-L17), [18](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L18-L18)

### [G-27]<a name="g-27"></a> Gas savings can be achieved by changing the model for assigning value to the structure

Here's an example to illustrate this:

```solidity
struct MyStruct {
  uint256 a;
  uint256 b;
  uint256 c;
}

function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
  MyStruct memory myStruct = MyStruct(_a, _b, _c);
  // Do something with myStruct
}
```

In this example, we have a simple MyStruct data structure with three uint256 fields. The assignValuesToStruct function takes three input parameters _a, _b, and _c, and assigns them to the corresponding fields in a new myStruct variable. This is done using the struct constructor syntax, which creates a new instance of the MyStruct struct with the specified field values.

The following approach can be more efficient than assigning values to the struct fields:

```solidity
function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
  MyStruct memory myStruct;
  myStruct.a = _a;
  myStruct.b = _b;
  myStruct.c = _c;
  // Do something with myStruct
}
```

By changing the pattern of assigning value to the structure, gas savings of ~130 per instance are achieved. In addition, this use will provide significant savings in distribution costs.

*There are 8 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

108:         plotMetadata[mainAccount] = PlotMetadata({
109:             lastUpdated: block.timestamp,
110:             currentTaxRate: DEFAULT_TAX_RATE
111:         });

120:             plotMetadata[landlord] = PlotMetadata({
121:                 lastUpdated: block.timestamp,
122:                 currentTaxRate: DEFAULT_TAX_RATE
123:             });

154:         plotOccupied[landlord][plotId] = Plot({
155:             occupied: true,
156:             tokenId: tokenId
157:         });

162:         toilerState[tokenId] = ToilerState({
163:             lastToilDate: block.timestamp,
164:             plotId: plotId,
165:             landlord: landlord,
166:             latestTaxRate: plotMetadata[landlord].currentTaxRate,
167:             dirty: false
168:         });

181:         plotOccupied[_toiler.landlord][_toiler.plotId] = Plot({
182:             occupied: false,
183:             tokenId: 0
184:         });

185:         toilerState[tokenId] = ToilerState({
186:             lastToilDate: 0,
187:             plotId: 0,
188:             landlord: address(0),
189:             latestTaxRate: 0,
190:             dirty: false
191:         });

215:         plotOccupied[_toiler.landlord][oldPlotId] = Plot({
216:             occupied: false,
217:             tokenId: 0
218:         });

219:         plotOccupied[_toiler.landlord][plotId] = Plot({
220:             occupied: true,
221:             tokenId: tokenId
222:         });

```


*GitHub* : [108](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L108-L111), [120](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L120-L123), [154](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L154-L157), [162](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L162-L168), [181](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L181-L184), [185](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L185-L191), [215](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L215-L218), [219](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L219-L222)

### [G-28]<a name="g-28"></a> Optimize Storage with Byte Truncation for Time Related State Variables

Storage optimization in Solidity contracts is vital for reducing gas costs, especially when storing time-related state variables. Using `uint32` for storing time values like timestamps is often sufficient, given it can represent dates up to the year 2106. By truncating larger default integer types to `uint32`, you significantly save on storage space and consequently on gas costs for deployment and state modifications. However, ensure that the truncation does not lead to overflow issues and that the variable's size is adequate for the application's expected lifespan and precision requirements. Adopting this optimization practice contributes to more efficient and cost-effective smart contract development.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

241:         uint256 timestamp;

```


*GitHub* : [241](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L241-L241)

### [G-29]<a name="g-29"></a> Trade-offs Between Modifiers and Internal Functions

In Solidity, both modifiers and internal functions can be used to modularize and reuse code, but they have different trade-offs.

Modifiers are primarily used to augment the behavior of functions, often for checks or validations. They can access parameters of the function they modify and are integrated into the function’s code at compile time. This makes them syntactically cleaner for repetitive precondition checks. However, modifiers can sometimes lead to less readable code, especially when the logic is complex or when multiple modifiers are used on a single function.

Internal functions, on the other hand, offer more flexibility. They can contain complex logic, return values, and be called from other functions. This makes them more suitable for reusable chunks of business logic. Since internal functions are separate entities, they can be more readable and easier to test in isolation compared to modifiers.

Using internal functions can result in slightly more gas consumption, as it involves an internal function call. However, this cost is usually minimal and can be a worthwhile trade-off for increased code clarity and maintainability.

In summary, while modifiers offer a concise way to include checks and simple logic across multiple functions, internal functions provide more flexibility and are better suited for complex and reusable code. The choice between the two should be based on the specific use case, considering factors like code complexity, readability, and gas efficiency.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

50:     function _reconfigure() internal {

232:     function _farmPlots(address _sender) internal {

312:     function _removeTokenIdFromStakedList(
313:         address mainAccount,
314:         uint256 tokenId
315:     ) internal {

332:     function _getMainAccountRequireRegistered(
333:         address _account
334:     ) internal view returns (address, MunchablesCommonLib.Player memory) {

344:     function _getNumPlots(address _account) internal view returns (uint256) {

```


*GitHub* : [50](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L50-L50), [232](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L232-L232), [312](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L312-L315), [332](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L332-L334), [344](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L344-L344)

### [G-30]<a name="g-30"></a> Divisions can be `unchecked` to save gas

The expression `type(int).min/(-1)` is the only case where division causes an overflow. Therefore, uncheck can be used to [save gas](https://gist.github.com/DadeKuma/3bc597338ae774b8b3bd43280d55271f) in scenarios where it is certain that such an overflow will not occur.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

284:                 (int256(schnibblesTotal) +
285:                     (int256(schnibblesTotal) * finalBonus)) / 100

288:                 (schnibblesTotal * _toiler.latestTaxRate) /
289:                 1e18;

345:         return lockManager.getLockedWeightedValue(_account) / PRICE_PER_PLOT;

```


*GitHub* : [284](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L284-L285), [288](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L288-L289), [345](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L345-L345)

### [G-31]<a name="g-31"></a> Using assembly to check for `address(0)` can save gas

Using assembly to check for `address(0)` can save gas by allowing more direct access to the evm and reducing some of the overhead associated with high-level operations in solidity.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

178:         if (_toiler.landlord == address(0)) revert NotStakedError();

207:         if (_toiler.landlord == address(0)) revert NotStakedError();

```


*GitHub* : [178](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L178-L178), [207](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L207-L207)

### [G-32]<a name="g-32"></a> Use assembly to `emit` events

To efficiently `emit` events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.
However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.
A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.

*There are 8 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

100:         emit TaxRateChanged(landlord, oldTaxRate, newTaxRate);

113:         emit UpdatePlotsMeta(mainAccount);

128:         emit UpdatePlotsMeta(landlord);

170:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

196:         emit FarmPlotLeave(_toiler.landlord, tokenId, _toiler.plotId);

224:         emit FarmPlotLeave(_toiler.landlord, tokenId, oldPlotId);

225:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

301:             emit FarmedSchnibbles(
302:                 _toiler.landlord,
303:                 tokenId,
304:                 _toiler.plotId,
305:                 schnibblesTotal - schnibblesLandlord,
306:                 schnibblesLandlord
307:             );

```


*GitHub* : [100](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L100-L100), [113](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L113-L113), [128](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L128-L128), [170](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L170-L170), [196](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L196-L196), [224](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L224-L224), [225](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L225-L225), [301](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L301-L307)

### [G-33]<a name="g-33"></a> Simple checks for zero can be done using assembly to save gas



*There are 6 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

96:         if (plotMetadata[landlord].lastUpdated == 0)

106:         if (plotMetadata[mainAccount].lastUpdated != 0)

119:         if (plotMetadata[landlord].lastUpdated == 0) {

178:         if (_toiler.landlord == address(0)) revert NotStakedError();

207:         if (_toiler.landlord == address(0)) revert NotStakedError();

340:         if (_player.registrationDate == 0) revert PlayerNotRegisteredError();

```


*GitHub* : [96](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L96-L96), [106](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L106-L106), [119](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L119-L119), [178](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L178-L178), [207](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L207-L207), [340](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L340-L340)

### [G-34]<a name="g-34"></a> Use solady library where possible to save gas

The following OpenZeppelin imports have a Solady equivalent, as such they can be used to save GAS as Solady modules have been specifically designed to be as GAS efficient as possible

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

9: import "openzeppelin-contracts/contracts/token/ERC721/IERC721.sol";

```


*GitHub* : [9](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L9-L9)

### [G-35]<a name="g-35"></a> Variable declared within iteration

Please elaborate and generalise the following with detail and feel free to use your own knowledge and lmit ur words to 100 words please:

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

264:                 MunchablesCommonLib.Player memory landlordMetadata

```


*GitHub* : [264](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L264-L264)

### [G-36]<a name="g-36"></a> Enable IR-based code generation

Enabling Intermediate Representation (IR)-based code generation in Solidity, via the --via-ir compiler flag or setting {'viaIR': true} in the configuration, activates an advanced optimization phase. This approach allows the compiler to perform more sophisticated optimizations across multiple functions, potentially leading to significant gas savings. IR-based compilation can optimize contract bytecode more efficiently, making smart contracts cheaper to deploy and execute by reducing the gas required for transactions

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

11: contract LandManager is BaseBlastManagerUpgradeable, ILandManager {

```


*GitHub* : [11](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L11-L11)

### [G-37]<a name="g-37"></a> Using XOR (^) and AND (&) bitwise equivalents for gas optimizations

Given 4 variables a, b, c and d represented as such:

```

0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d

```

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

*There are 7 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

96:         if (plotMetadata[landlord].lastUpdated == 0)

119:         if (plotMetadata[landlord].lastUpdated == 0) {

137:         if (landlord == mainAccount) revert CantStakeToSelfError();

178:         if (_toiler.landlord == address(0)) revert NotStakedError();

207:         if (_toiler.landlord == address(0)) revert NotStakedError();

319:             if (munchablesStaked[mainAccount][i] == tokenId) {

340:         if (_player.registrationDate == 0) revert PlayerNotRegisteredError();

```


*GitHub* : [96](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L96-L96), [119](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L119-L119), [137](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L137-L137), [178](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L178-L178), [207](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L207-L207), [319](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L319-L319), [340](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L340-L340)

