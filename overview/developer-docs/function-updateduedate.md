# Function updatedueDate()

A payment can be initiated with a 0 `_dueDate`. This functionality allows for other use cases than just early payment of a payment request or invoice. One could use the smart contract as a sort of escrow service, where users can release the payment by updating the due date of the payment.

```solidity
function updateDueDate(bytes calldata _paymentReference, uint256 _dueDateUpdated) public IsInContract(_paymentReference) OnlyPayer(_paymentReference) nonReentrant{
        require(paymentMapping[_paymentReference].dueDate == 0, "New due date != 0");
        require(_dueDateUpdated > block.timestamp + 1200 && _dueDateUpdated <= block.timestamp + (maxDueDateInDays * 86400), "Invalid new due date");
        paymentMapping[_paymentReference].dueDate = _dueDateUpdated;
        address _payee = paymentMapping[_paymentReference].payee;
 
        emit DueDateUpdatedEvent(msg.sender, _payee, _paymentReference, _dueDateUpdated);
    }
```

| Parameter           | Description                                                                                                                                                                                          |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_paymentReference` | Needs to be inserted in bytes                                                                                                                                                                        |
| `_dueDateUpdated`   | Insert the new due date in Epoch time. Make sure the new due date is greater than the current block.timestamp + 1200 seconds (100 blocks) and smaller than the current `maxDueDateInDays` parameter. |

This function uses 2 modifiers:

```solidity
IsInContract(_paymentReference)
```

Checks if the `_paymentReference` is present in the `paymentMapping`.

```solidity
OnlyPayer(_paymentReference)
```

Only allows the payer of the `paymentReference` to change the `dueDate` .

The payer can only update the due date of the payment reference once.
