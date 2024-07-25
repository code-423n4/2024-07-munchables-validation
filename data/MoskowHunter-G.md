1. 
Is it possible here to use mapping instead of looping? Avoid a loop and directly accesses to the index of removed token.

LandManager.sol

function _removeTokenIdFromStakedList(
        address mainAccount,
        uint256 tokenId
    ) internal {
        uint256 stakedLength = munchablesStaked[mainAccount].length;
        bool found = false;
        for (uint256 i = 0; i < stakedLength; i++) {
            if (munchablesStaked[mainAccount][i] == tokenId) {
                munchablesStaked[mainAccount][i] = munchablesStaked[
                    mainAccount
                ][stakedLength - 1];
                found = true;
                munchablesStaked[mainAccount].pop();
                break;
            }
        }

        if (!found) revert InvalidTokenIdError();
    }

possible solution or at least the Idea:

mapping(address => mapping(uint256 => uint256)) private tokenIndex; 

function _removeTokenIdFromStakedList(
    address mainAccount,
    uint256 tokenId
) internal {
    uint256 index = tokenIndex[mainAccount][tokenId];
    uint256 stakedLength = munchablesStaked[mainAccount].length;

    if (index >= stakedLength) revert InvalidTokenIdError(); 

    if (index < stakedLength - 1) {
        uint256 lastTokenId = munchablesStaked[mainAccount][stakedLength - 1];
        munchablesStaked[mainAccount][index] = lastTokenId;
        tokenIndex[mainAccount][lastTokenId] = index;
    }

    munchablesStaked[mainAccount].pop();
    delete tokenIndex[mainAccount][tokenId];
}

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/config/ConfigStorage.sol#L98

2.

function getUintArray(
    StorageKey _key
) public view returns (uint256[] memory _uintArray) {
    uint256[] memory uintArray = new uint256[](uintArrayStorageLength[_key]);
    for (uint8 i; i < uintArrayStorageLength[_key]; i++) {
        bytes32 encodedKey = keccak256(abi.encodePacked(_key, i));
        uintArray[i] = uintArrayStorage[encodedKey];
    }
    _uintArray = uintArray;
}

The key is re-hashed with keccak256(abi.encodePacked(_key, i)) in every iteration.

function getUintArray(
    StorageKey _key
) public view returns (uint256[] memory) {
    uint8 length = uintArrayStorageLength[_key];
    uint256[] memory uintArray = new uint256[](length);

    bytes32 baseKey = keccak256(abi.encodePacked(_key));

    for (uint8 i = 0; i < length; i++) {
        bytes32 encodedKey = keccak256(abi.encodePacked(baseKey, i));
        uintArray[i] = uintArrayStorage[encodedKey];
    }
    return uintArray;
}

keccak256(abi.encodePacked(_key)) is hashed once and reused within the loop with keccak256(abi.encodePacked(baseKey, i))

also "return" can imrove readability an minimal minor gas saving.
