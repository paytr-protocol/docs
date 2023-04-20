# Function payInvoiceERC20WithFee()

The `payInvoiceERC20` function allows users and other contracts to make an early payment and start earning yield, while allowing an extra fee to be collected upfront.

```solidity
function payInvoiceERC20WithFee(
        address _asset, //address base token, like USDC
        address _payee,
        address _feeAddress,
        uint48 _dueDate,
        uint256 _amount,
        uint256 _feeAmount,
        bytes calldata _paymentReference,
        address _cometAddress
        ) public IsNotPaid(_paymentReference) nonReentrant whenNotPaused
```

| Parameter           | Description                                                                                                                                                                                                                                                                                                                                            |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `_asset`            | The base token. Make sure the ERC20 token is [supported by Compound Finance](https://docs.compound.finance/#networks). The base asset also needs to be used in conjunction with the correct Comet address. Using the wrong asset for the Comet address or vice versa will result in an error.                                                          |
| `_payee`            | The receiver of the principal amount.                                                                                                                                                                                                                                                                                                                  |
| `_feeAddress`       | <p>In case you want to charge an extra fee, this is the receiver address of the fee.<br>If the <code>_feeAmount</code> is 0, the smart contract will use the msg.sender address as <code>_feeAddress</code><em>.</em> Make sure to pass the msg.sender as parameter if the feeAmount is 0, to prevent an error on the number of parameters passed.</p> |
| `_dueDate`          | The due date of the payment in days. Please note this is subject to change. It is possible to insert a `_dueDate` of 0.                                                                                                                                                                                                                                |
| `_amount`           | The principal amount in wei. Make sure to double check the number of decimals for the `_asset` you're passing.                                                                                                                                                                                                                                         |
| `_feeAmount`        | The fee amount in wei. Again, make sure to double check the number of decimals for the `_asset`.                                                                                                                                                                                                                                                       |
| `_paymentReference` | Needs to be passed in bytes.                                                                                                                                                                                                                                                                                                                           |
| `_cometAddress`     | The address of Compound Finance's V3 cToken contract. You can check the Comet address per chain and asset [here](https://docs.compound.finance/#networks). The Comet contract address is the first one in the list, for example cUSDCv3.                                                                                                               |

{% hint style="info" %}
The function uses modifier `IsNotPaid(_paymentReference)`.
{% endhint %}

The `_amount` and `_feeAmount` are transferred to the Comet contract, to earn yield.\
The payment details are stored in the `paymentMapping.`
