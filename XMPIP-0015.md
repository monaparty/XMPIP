<pre>
  XMPIP: 0015
  Title: Add "trigger" message
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Created: 2020-11-15
</pre>

## Abstract ##

Counterparty has been receiving multiple fixes and enhancements,
and some more contracts are planned.
They will have complext state and will require their specific messages (trigger) to change their state.
On the other hand, message ID is a limited resouce.
It should be provided a unified message format.
The "trigger" message solves this by sending `tx_hash` and the binary message.

## Motivation ##

Some more contracts are going to be planned on Monaparty like auction and escrow.
In such contructs, it may be required some kinds of simple triggers that changes their state.

## Rationale ##

Some contracts that have their state may require some trigger messages to move transition their state.
But message ID is a limited resource. It should be provided a unified message.

Existing contracts with their state are identified with `tx_hash`.
This is the reason why `cancel` message effects to various contracts (currently `order`, `bets` and `rps`) in the same API.
But `cancel` is just for canceling action. It's not for general-purpose.

The requirement can be resolved by providing the new message that have the target `tx_hash` and additinal data like `memo` in `send`.

## Technical implementation ##

A new message type under the CIP11 numbering should be allocated for address
sweep messages.

The address sweep must specify the following fields:

  * New Message ID: ````0x78````
  * tx_hash (32bytes)
  * Data field (Rest of the remaining message)

The trigger message itself doesn't requrire any anti-spam fee.
But contracts trigged by this message may require an anti-spam fee from the sending address.

Similar to `cancel`, the validation process of this this message depends on other contracts.

# API Changes

### create_trigger

`create_trigger` creates a trigger message:

 * **destination** (string): The address receiving the assets and/or ownerships.
 * **tx_hash** (string): transaction hash that is expected to affect. a hexadecimal value.
 * **data** (string): data
 * **data_is_hex** (boolean): If this is true, interpret the data as a hexadecimal value. Defaults to false.

### get_triggers

gets the triggers from the database, in the same manner as other get_* ops.

# Copyright

This document is placed in the public domain.
