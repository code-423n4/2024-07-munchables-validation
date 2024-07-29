## Summary

| |Issue|Instances| 
|-|:-|:-:|
| [[L-01](#l-01)] | Consider adding validation of user inputs | 3| 
| [[L-02](#l-02)] | Consider bounding input array length | 1| 
| [[L-03](#l-03)] | Privileged functions can create points of failure | 2| 
| [[L-04](#l-04)] | Code does not follow the best practice of check-effects-interaction | 1| 
| [[L-05](#l-05)] | Events may be emitted out of order due to reentrancy | 3| 
| [[L-06](#l-06)] | External calls in an unbounded loop can result in a DoS | 2| 
| [[L-07](#l-07)] | Functions calling contracts/addresses with transfer hooks are missing reentrancy guards | 2| 
| [[L-08](#l-08)] | Mapping arrays can grow in size without a way to shrink them | 1| 
| [[L-09](#l-09)] | Using zero as a parameter | 2| 

### Low Risk Issues

### [L-01]<a name="l-01"></a> Consider adding validation of user inputs

There are no validations done on the arguments below. Consider that the Solidity [documentation](https://docs.soliditylang.org/en/latest/control-structures.html#panic-via-assert-and-error-via-require) states that `Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix`. This means that there should be explicit checks for expected ranges of inputs. Underflows/overflows result in panics should not be used as range checks, and allowing funds to be sent to `0x0`, which is the default value of address variables and has many gotchas, should be avoided.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit missing checks for -->  _configStorage
45:     function initialize(address _configStorage) public override initializer {

// @audit missing checks for -->  landlord
116:     function updatePlotMetadata(
117:         address landlord
118:     ) external override onlyConfiguredContract(StorageKey.AccountManager) {

// @audit missing checks for -->  landlord
131:     function stakeMunchable(
132:         address landlord,
133:         uint256 tokenId,
134:         uint256 plotId
135:     ) external override forceFarmPlots(msg.sender) notPaused {

```


*GitHub* : [45](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L45), [116](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L116-L118), [131](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L131-L135)

### [L-02]<a name="l-02"></a> Consider bounding input array length

Unbounded array inputs in functions can lead to unintentional excessive gas consumption, potentially causing a transaction to revert after expending substantial gas. To enhance user experience and prevent such scenarios, consider implementing a `require()` statement that limits the array length to a defined maximum. This constraint ensures that transactions won't proceed if they're likely to hit gas limits due to array size, saving users from unnecessary gas costs and offering a more predictable interaction with the contract.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

//@audit staked.length not bounded 
247:         for (uint8 i = 0; i < staked.length; i++) {

```


*GitHub* : [247](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L247)

### [L-03]<a name="l-03"></a> Privileged functions can create points of failure

Ensure such accounts are protected and consider implementing multi sig to prevent a single point of failure

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

88:     function configUpdated() external override onlyConfigStorage {

116:     function updatePlotMetadata(
117:         address landlord
118:     ) external override onlyConfiguredContract(StorageKey.AccountManager) {

```


*GitHub* : [88](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L88), [116](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L116-L118)

### [L-04]<a name="l-04"></a> Code does not follow the best practice of check-effects-interaction

Code should follow the best-practice of [CEI](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit munchNFT.transferFrom() called on line 152 
160:         munchableOwner[tokenId] = mainAccount;

```


*GitHub* : [160](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L160)

### [L-05]<a name="l-05"></a> Events may be emitted out of order due to reentrancy

If a reentrancy occurs, some events may be emitted in an unexpected order, and this may be a problem if a third party expects a specific order for these events. Ensure that events are emitted before external calls and follow the best practice of CEI.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

170:         emit FarmPlotTaken(toilerState[tokenId], tokenId);

196:         emit FarmPlotLeave(_toiler.landlord, tokenId, _toiler.plotId);

301:             emit FarmedSchnibbles(
302:                 _toiler.landlord,
303:                 tokenId,
304:                 _toiler.plotId,
305:                 schnibblesTotal - schnibblesLandlord,
306:                 schnibblesLandlord
307:             );

```


*GitHub* : [170](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L170), [196](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L196), [301](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L301-L307)

### [L-06]<a name="l-06"></a> External calls in an unbounded loop can result in a DoS

Consider limiting the number of iterations in loops that make external calls, as just a single one of them failing will result in a revert.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

265:             ) = _getMainAccountRequireRegistered(landlord);

300:             accountManager.updatePlayer(landlord, landlordMetadata);

```


*GitHub* : [265](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L265), [300](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L300)

### [L-07]<a name="l-07"></a> Functions calling contracts/addresses with transfer hooks are missing reentrancy guards

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

// @audit function 'stakeMunchable()' is missing Reentrancy guard
152:         munchNFT.transferFrom(mainAccount, address(this), tokenId);

// @audit function 'unstakeMunchable()' is missing Reentrancy guard
195:         munchNFT.transferFrom(address(this), mainAccount, tokenId);

```


*GitHub* : [152](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L152), [195](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L195)

### [L-08]<a name="l-08"></a> Mapping arrays can grow in size without a way to shrink them

It's a good practice to maintain control over the size of array mappings in Solidity, especially if they are dynamically updated. If a contract includes a mechanism to push items into an array, it should ideally also provide a mechanism to remove items. This is because Solidity arrays don't automatically shrink when items are deleted - their length needs to be manually adjusted.

Ignoring this can lead to bloated and inefficient contracts. For instance, iterating over a large array can cause your contract to hit the block gas limit. Additionally, if entries are only marked for deletion but never actually removed, you may end up dealing with stale or irrelevant data, which can cause logical errors.

Therefore, implementing a method to 'pop' items from mapping arrays helps manage contract's state, improve efficiency and prevent potential issues related to gas limits or stale data. Always ensure to handle potential underflow conditions when popping elements from the mapping array.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

27:     mapping(address => uint256[]) munchablesStaked;

```


*GitHub* : [27](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L27)

### [L-09]<a name="l-09"></a> Using zero as a parameter

Passing `0` or `0x0` as a function argument can sometimes result in a security issue(e.g. passing zero as the slippage parameter). A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because `0` can be interpreted as an uninitialized address, leading to transfers to the `0x0` address, effectively burning tokens. Moreover, `0` as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code.

Consider using a constant variable with a descriptive name, so it's clear that the argument is intentionally being used, and for the right reasons.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/managers/LandManager.sol

181:         plotOccupied[_toiler.landlord][_toiler.plotId] = Plot({
182:             occupied: false,
183:             tokenId: 0
184:         });

215:         plotOccupied[_toiler.landlord][oldPlotId] = Plot({
216:             occupied: false,
217:             tokenId: 0
218:         });

```


*GitHub* : [181](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L181-L184), [215](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L215-L218)

