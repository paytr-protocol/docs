# Function payOutERC20Invoice()

This function handles the payment of all due payment references and payout of additional fees.

Anyone can call this function by passing in a `bytes` array of due payment references. Make sure to respect the max length of the array by checking the `maxPayoutArraySize` parameter. This parameter is set to prevent hitting the block gas limit.

#### Request Network compatibility

To ensure the compatibility with [Request Network](https://www.request.network), the payout function checks the `_feeAddress`.\
When the check passes, Request's [`ERC20FeeProxy`](https://github.com/RequestNetwork/requestNetwork/blob/master/packages/smart-contracts/src/contracts/ERC20FeeProxy.sol) gets called.

```solidity
function payOutERC20Invoice(bytes[] calldata payoutReferencesArray) external nonReentrant {
 
        uint256 i;
        uint256 payoutReferencesArrayLength = payoutReferencesArray.length;
 
        require(payoutReferencesArrayLength != 0 && payoutReferencesArrayLength <= maxPayoutArraySize, "Invalid array length");
 
        for (; i < payoutReferencesArrayLength;) {
            bytes memory _paymentReference = payoutReferencesArray[i];
            require(paymentMapping[_paymentReference].amount != 0,"Unknown payment reference"); //check to see if payment reference to be paid out is present in the contract
            require(paymentMapping[_paymentReference].dueDate <= block.timestamp,"Reference not due yet");
 
            address payable _payee = payable(paymentMapping[_paymentReference].payee);
            address payable _payer = payable(paymentMapping[_paymentReference].payer);
            address payable _feeAddress = payable(paymentMapping[_paymentReference].feeAddress);
            uint256 _amount = paymentMapping[_paymentReference].amount;
            uint256 _feeAmount = paymentMapping[_paymentReference].feeAmount;
            uint256 _wrapperSharesToRedeem = paymentMapping[_paymentReference].wrapperSharesReceived;
 
            paymentMapping[_paymentReference].amount = 0; //prevents double payout because of the require statement
 
            //redeem Wrapper shares and receive v3 cTokens
            IWrapper(wrapperAddress).redeem(_wrapperSharesToRedeem, address(this), address(this));
 
            //redeem all available v3 cTokens from Compound for baseAsset tokens
            uint256 cTokensToRedeem = IERC20(cometAddress).balanceOf(address(this));
            IComet(cometAddress).withdraw(baseAsset, cTokensToRedeem);
 
            //get new USDC balance
            uint256 _totalInterestGathered = IERC20(baseAsset).balanceOf(address(this)) - _amount;
            uint256 _interestAmount = _totalInterestGathered * (10000 - (feeModifier*100)) / 10000;
 
            if(allowedRequestNetworkFeeAddresses[_feeAddress] == true) {
                IERC20(baseAsset).safeApprove(ERC20FeeProxyAddress, _amount + _feeAmount);
 
                IERC20FeeProxy(ERC20FeeProxyAddress).transferFromWithReferenceAndFee(
                    baseAsset,
                    _payee,
                    _amount,
                    _paymentReference,
                    _feeAmount,
                    _feeAddress
                );
              
            } else {
                IERC20(baseAsset).safeTransfer(_payee, _amount);
                if(_feeAmount != 0) {
                    IERC20(baseAsset).safeTransfer(_feeAddress, _feeAmount);
                }
            }
          
            IERC20(baseAsset).safeTransfer(_payer, _interestAmount);
            ++i;
 
            emit PayOutERC20Event(baseAsset, _payee, _feeAddress, _amount, _paymentReference, _feeAmount);
            emit InterestPayoutEvent(baseAsset, _payer, _interestAmount, _paymentReference);
       }
          
    }
```

