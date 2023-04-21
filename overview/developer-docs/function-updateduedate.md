# Function updatedueDate()

A payment can be initiated with a 0 `_dueDate`. This functionality allows for other use cases than just early payment after x amount of days. One could use the smart contract as a real escrow service, where users can release the payment by updating the due date of the payment.

```solidity
function updateDueDate(bytes calldata _paymentReference, uint256 _dueDateUpdated) public IsInContract(_paymentReference) OnlyPayer(_paymentReference) nonReentrant whenNotPaused {
        require(paymentMapping[_paymentReference].dueDate == 0, "Your payment reference already has a due date assigned");
        require(_dueDateUpdated >= block.timestamp + 1 days, "New due date needs to be > block.timestamp + 1 day");
        paymentMapping[_paymentReference].dueDate = _dueDateUpdated;
        address _payee = paymentMapping[_paymentReference].payee;

        emit DueDateUpdatedEvent(msg.sender, _payee, _paymentReference, _dueDateUpdated);
    }
```

`_paymentReference`: Needs to be inserted in bytes

`_dueDateUpdated`: Insert the new due date in Epoch time. Make sure the new due date is greater than the current block.timestamp + 1 day.

This function uses 2 modifiers:

```solidity
IsInContract(_paymentReference)
```

Checks if the `_paymentReference` is present in the `paymentMapping`.

```solidity
OnlyPayer(_paymentReference)
```

Only allows the payer to change the `dueDate` of the `paymentReference`.
