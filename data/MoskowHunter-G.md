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


