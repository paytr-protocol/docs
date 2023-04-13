---
description: Only callable by the contract owner and the automated Gelato smart contract
---

# Function payOutERC20Invoice()

This function handles the payment of all due payment references.\


Because of gas fees and block limits, the calculation of due references, interest amounts etc. happens off-chain, hence this function can only be called by the contract owner and the Gelato smart contract.\
The Gelato Web3 Function runs every minute during the beta, you can view the complete calculation here \[insert link]. The code is uploaded to IPFS and is immutable.\
Upon mainnet deployment, the Gelato function will run once or twice a day.\
We use Gelato to offer a much more decentralised solution. Anyone can see the code that gets used to calculate the payouts.

#### Request Network compatibility

To ensure the compatibility with [Request Network](https://www.request.network), the payout function checks the feeAddress.\
When the check passes, Request's `ERC20FeeProxy` gets called.

```solidity
function payOutERC20Invoice(RedeemDataERC20[] calldata redeemData, totalPerAssetToRedeem[] calldata assetsToRedeem ) public onlyGelato nonReentrant {  

        require(redeemData.length > 0 && assetsToRedeem.length > 0, "No payments to redeem");        
        require(redeemFundsERC20(assetsToRedeem), "Redeeming failed");

        uint i;
        uint256 redeemDataLength = redeemData.length;

        for (; i < redeemDataLength;) {
            bytes memory _paymentReference = redeemData[i].paymentReference;
            address payable _payee = payable(redeemData[i].payee);
            address payable _payer = payable(redeemData[i].payer);
            address payable _feeAddress = payable(redeemData[i].feeAddress);
            address _asset = redeemData[i].asset;            
            uint256 _amount = redeemData[i].amount;
            uint256 _feeAmount = redeemData[i].feeAmount;
            
            //Update state before transferring funds
            delete paymentMapping[_paymentReference];
            
            uint256 _interestAmount = redeemData[i].interestAmount * 9000 / 10000;      
            
            /*
            Transfer funds to payer, payee and feeAddress. Payments originating from Request Network call the ERC20FeeProxy contract.
            The Request Network payments are detected by checking the _feeAddress parameter.
            */
            if(allowedRequestNetworkFeeAddresses[_feeAddress] == true) {
                IERC20(_asset).safeApprove(ERC20FeeProxyAddress, _amount + _feeAmount);

                IERC20FeeProxy(ERC20FeeProxyAddress).transferFromWithReferenceAndFee(
                    _asset,
                    _payee,
                    _amount,
                    _paymentReference,
                    _feeAmount,
                    _feeAddress
                );
            
            } else {
                IERC20(_asset).safeTransfer(_payee, _amount);
                IERC20(_asset).safeTransfer(_feeAddress, _feeAmount); 
            }
            
            IERC20(_asset).safeTransfer(_payer, _interestAmount);
            ++i;     

            emit PayOutERC20Event(_asset, _payee, _amount, _paymentReference, _feeAmount, _feeAddress);           
        }        
    }
```
