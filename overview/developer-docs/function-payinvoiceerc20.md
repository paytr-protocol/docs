# Function payInvoiceERC20()

The `` `payInvoiceERC20` `` function allows users and other contracts to make an early payment and start earning yield.

````solidity
```solidity
function payInvoiceERC20(
        address _payee,
        address _feeAddress,
        uint8 _dueInDays,
        uint256 _amount,
        uint256 _feeAmount,
        bytes calldata _paymentReference
        ) public nonReentrant whenNotPaused {
```
````

<table><thead><tr><th>Parameter</th><th width="504.3333333333333">Description</th></tr></thead><tbody><tr><td><code>_payee</code></td><td>The receiver of the principal amount.</td></tr><tr><td>_feeAddress</td><td>In case you want to charge an extra fee, this is the receiving address of the fee.<br>If the <code>_feeAmount</code> is 0, the smart contract will use the msg.sender address as <code>_feeAddress</code><em>.</em> Make sure to pass the msg.sender as parameter if the feeAmount is 0, to prevent an error on the number of parameters passed.</td></tr><tr><td><code>_dueInDays</code></td><td>The due date of the payment in days. It is possible to insert a <code>_dueDate</code> of 0.</td></tr><tr><td><code>_amount</code></td><td>The principal amount in wei. Make sure to double check the number of decimals for the <code>_asset</code> you're passing.</td></tr><tr><td><code>_feeAmount</code></td><td>The fee amount in wei. Make sure to double check the number of decimals for the <code>_feeAmount</code> you're passing.</td></tr><tr><td><code>_paymentReference</code></td><td>Needs to be passed in bytes.</td></tr></tbody></table>

The `_amount` is transferred to the Comet contract, to earn yield.\
The payment details are stored in the `paymentMapping.`
