# Function payInvoiceERC20()

The `` `payInvoiceERC20` `` function allows users and other contracts to make an early payment and start earning yield.

```solidity
function payInvoiceERC20(
        address _asset, //address base token, like USDC
        address _payee,
        uint48 _dueDate,
        uint256 _amount,
        bytes calldata _paymentReference,
        address _cometAddress
        ) public IsNotPaid(_paymentReference) nonReentrant whenNotPaused
```

| Parameter           | Description                                                                                                                                                                                                                                                                                   |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_asset`            | The base token. Make sure the ERC20 token is [supported by Compound Finance](https://docs.compound.finance/#networks). The base asset also needs to be used in conjunction with the correct Comet address. Using the wrong asset for the Comet address or vice versa will result in an error. |
| `_payee`            | The receiver of the principal amount.                                                                                                                                                                                                                                                         |
| `_dueDate`          | The due date of the payment in days. Please note this is subject to change. It is possible to insert a `_dueDate` of 0.                                                                                                                                                                       |
| `_amount`           | The principal amount in wei. Make sure to double check the number of decimals for the `_asset` you're passing.                                                                                                                                                                                |
| `_paymentReference` | Needs to be passed in bytes.                                                                                                                                                                                                                                                                  |
| `_cometAddress`     | The address of Compound Finance's V3 cToken contract. You can check the Comet address per chain and asset [here](https://docs.compound.finance/#networks). The Comet contract address is the first one in the list, for example cUSDCv3.                                                      |

The function uses modifier `IsNotPaid(_paymentReference)`.

The `_amount` is transferred to the Comet contract, to earn yield.\
The payment details are stored in the `paymentMapping.`
