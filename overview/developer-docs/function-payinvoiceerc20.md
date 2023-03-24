# Function payInvoiceERC20

The \`payInvoiceERC20\` function is the most used function in the contract. It allows users to make an early payment and start earning yield.

```solidity
function payInvoiceERC20(
        address _asset, //address underlying token, like USDC
        address _payee,
        address _feeAddress,
        uint48 _dueDate,
        uint256 _amount,
        uint256 _feeAmount,
        bytes memory _paymentReference,
        address _cometAddress
        ) public IsNotPaid(_paymentReference) nonReentrant whenNotPaused {
```
