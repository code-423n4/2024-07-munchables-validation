## 1. Mainacct cannot unstake if munchNFT blocklisted

Links to affected code *

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/tokens/MunchNFT.sol#L78

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L195

### Impact

From the attack ideas part of the readme, a deadlock can occur for a user if their tokenid gets blocklisted. 
> The biggest concerns are people being able to unstake a different individual's Munchables or deadlocks (ie. where an individual's Munchable is stuck).
In MunchNFT.sol, there's a `blToken` function in which an account can be blocklisted. As a result, transfers are not possible for such users.

```solidity
    function blToken(uint256 _tokenId) external onlyAdmin {
        _blacklistToken[_tokenId] = true;
    }

```

If the tokenId gets blocklisted while being staked, the mainAccount will not be able to unstake their nft.

```solidity
    function unstakeMunchable(
        uint256 tokenId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...

        munchNFT.transferFrom(address(this), mainAccount, tokenId);
        emit FarmPlotLeave(_toiler.landlord, tokenId, _toiler.plotId);
    }
```

I'm pointing this out because, on the contrary, if a user gets blocklisted through the `blAccount`, they can still unstake their tokens. 

```solidity
    function blAccount(address _account) external onlyAdmin {
        _blacklistAccount[_account] = true;
    }
```
This is because the `_update` function checks if the sender or tokenid is blocklisted, and in this case, the sender is the LandManager.

```solidity
    function _update(
        address _to,
        uint256 _tokenId,
        address _auth
    ) internal override(ERC721, ERC721Enumerable) notPaused returns (address) {
        address from = _ownerOf(_tokenId);
        munchadexManager.updateMunchadex(from, _to, _tokenId);
        // check the token or the sender are not blacklisted
        if (_blacklistAccount[from] || _blacklistToken[_tokenId])
            revert ForbiddenTransferError();

        return super._update(_to, _tokenId, _auth);
    }
```
### Recommended Mitigation Steps

***

## 2. Last landlord's plot cannot be staked or transferred into.

Links to affected code *

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L146

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L211

### Impact

In the `stakeMunchable` function, there's a check to ensure that the plotid to stake into is not more than the number of plots the user has. The check however uses the >= operator before it reverts.

```solidity
    function stakeMunchable(
        address landlord,
        uint256 tokenId,
        uint256 plotId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
        uint256 totalPlotsAvail = _getNumPlots(landlord);
        if (plotId >= totalPlotsAvail) revert PlotTooHighError();
//...
    }
```
The same can be observed in the `transferToUnoccupiedPlot` function. 
```solidity
    function transferToUnoccupiedPlot(
        uint256 tokenId,
        uint256 plotId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
        if (plotId >= totalPlotsAvail) revert PlotTooHighError();
//...
    }
```
Since a non-existent or [empty plotid is denoted by 0](https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L187), this means is that plotids start from 1 and as a result, a landlord's last plotid will be the total number of plots he has, i.e `totalPlotsAvail`. So, the check above will cause that the function will always revert when the token is attempted to be staked or transferred to the last plot id of the landlord. If the landlord has only 1 plotid, tokens cannot be staked on it.

### Recommended Mitigation Steps

Recommend making the following changes

```diff
    function stakeMunchable(
        address landlord,
        uint256 tokenId,
        uint256 plotId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
        uint256 totalPlotsAvail = _getNumPlots(landlord);
-       if (plotId >= totalPlotsAvail) revert PlotTooHighError();
+       if (plotId > totalPlotsAvail) revert PlotTooHighError();
//...
    }
```
The same can be observed in the `transferToUnoccupiedPlot` function. 
```diff
    function transferToUnoccupiedPlot(
        uint256 tokenId,
        uint256 plotId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
-       if (plotId >= totalPlotsAvail) revert PlotTooHighError();
+       if (plotId > totalPlotsAvail) revert PlotTooHighError();
//...
    }
```
***

## 3. Redundant check for approval before `transferFrom` when staking

Links to affected code *

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L148-L153

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dbb6104ce834628e473d2173bbc9d47f81a9eec3/contracts/token/ERC721/ERC721.sol#L245-L247

### Impact

In `stakeMunchable`, there's a check for approval before the `transferFrom` function is called. The approval check is redundant, not needed, because the internal [`transferFrom`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dbb6104ce834628e473d2173bbc9d47f81a9eec3/contracts/token/ERC721/ERC721.sol#L143) function also performs the same check in the [`_update`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dbb6104ce834628e473d2173bbc9d47f81a9eec3/contracts/token/ERC721/ERC721.sol#L245-L247) function making the check redundant.

```solidity
    function stakeMunchable(
        address landlord,
        uint256 tokenId,
        uint256 plotId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
        if (
            !munchNFT.isApprovedForAll(mainAccount, address(this)) &&
            munchNFT.getApproved(tokenId) != address(this)
        ) revert NotApprovedError();
        munchNFT.transferFrom(mainAccount, address(this), tokenId);

//...
    }
```
### Recommended Mitigation Steps

Consider removing the check for approval.


## 4. Functions to get tokens out of protocol should not be pausable

Links to affected code *

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L175

### Impact

In real time scenarios, the functionality to pause unstakes can serve as a double edge sword and can be maliciously used to stop users from being able to claim their tokens. Hence a general rule of thumb to ensure safety is to make sure functions that help get tokens out of a protocol i.e claim, withdraw, redeem etc are not pausable.

```solidity
    function unstakeMunchable(
        uint256 tokenId
    ) external override forceFarmPlots(msg.sender) notPaused {
//...
    }
```
### Recommended Mitigation Steps

Recommend removing the `notPaused` modifier from `unstakeMunchable` function.

***
