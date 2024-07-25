## Impact
Detailed description of the impact of this finding.
newTaxRate and oldTaxRate can be the same.
## Proof of Concept
Provide direct links to all referenced code in GitHub. Add screenshots, logs, or any other relevant proof that illustrates the concept.
 function updateTaxRate(uint256 newTaxRate) external override notPaused {
        (address landlord, ) = _getMainAccountRequireRegistered(msg.sender);
        if (newTaxRate < MIN_TAX_RATE || newTaxRate > MAX_TAX_RATE)
            revert InvalidTaxRateError();
        if (plotMetadata[landlord].lastUpdated == 0)
            revert PlotMetadataNotUpdatedError();
        uint256 oldTaxRate = plotMetadata[landlord].currentTaxRate;
        plotMetadata[landlord].currentTaxRate = newTaxRate;
        emit TaxRateChanged(landlord, oldTaxRate, newTaxRate);
    }


## Tools Used

##code snippet
https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L92

## Recommended Mitigation Steps

require(oldTaxRate!=newTaxRate);
