# 1: Spelling Errors

Vulnerability details

## Proof of Concept

> ***File: LandManager.sol*** 

doesnt and hasnt can be changed to doesn't and hasn't in the following comment.

https://github.com/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L255

### Tools Used

Manual Analysis

# 2: FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGE

Vulnerability details

## Context:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces to allow us to apply this rule better.

## Proof of Concept

### Contracts 

> ***File: src/manager/landManager.sol***

https://github.com/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L4-L9

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

import {contract1 , contract2} from "filename.sol";

#### Example:

import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
