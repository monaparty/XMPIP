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

Monapparty has been receiving multiple fixes and enhancements,
and some more contracts are planned.
They will have a complex state and will require their specific messages (trigger) to change their state.

On the other hand, message ID is a limited resouce.
It is required to provide a unified and plasticity message format.
The "trigger" message solves this requirement.
Trigger contract dispatches the binary in the message to the Monaparty contracts that is bound by `tx_hash`.

## Motivation ##

Some more contracts are going to be planned on Monaparty like auction, escrow, and automation.
By such contructs, they require some kinds of simple triggers that change their state.

## Rationale ##

Contracts may require some trigger messages to move transition their state.
But message ID is a limited resource.
It should be provided a unified and plasticity message bucket.

The requirement can be resolved by providing the new message that have the target `tx_hash` and additinal data like `memo` in `send`.

Existing contracts with their state are identified with `tx_hash`.
This is the reason why `cancel` message effects to various contracts (currently `order`, `bets` and `rps`) in the same API.
But `cancel` is just for canceling action. It's not for general-purpose.

Some existing Monaparty objects are using broadcast message for changing their state.
But each broadcast message contains a text message in UTF-8, not binary. And it doesn't have the placeholder for `tx_hash`.

So both cancel and broadcast message are not fit to the requirement of this XMPIP.

## Technical implementation ##

A new message type under the CIP11 numbering should be allocated for trigger messages.

The trigger message must specify the following fields:

  * New Message ID: ````0x78````
  * target_hash (32bytes)
  * Payload field (Rest of the remaining message)

The trigger message itself requrires 0.001 XMP as the trigger fee. It means also anti spam fee.
This fee isn't back even if the message is invalidate in the triggered contract identified `target_hash`. 
The validator will invalidate the message if the source address have less XMP than the relay fee.

In addition, contracts triggered by this message may require more anti spam fee from the source address.

Similar to `cancel`, the validation process of the trigger depends on the validator in the target contract.

# Changes

## API Changes

### create_trigger

`create_trigger` creates a trigger message:

 * **source** (string): The address triggering contract that identified by `tx_hash`.
 * **target_hash** (string): transaction hash that is expected to affect. ASCII string that can interpret a hexadecimal value.
 * **payload** (string): data that is transferred to the trigger receiver
 * **payload_is_hex** (boolean): If this is true, interpret the payload as a hexadecimal value. Defaults to false.

### get_triggers

gets the triggers from the database, in the same manner as other get_* ops.

## DB changes

`triggers` table is added.

```
CREATE TABLE IF NOT EXISTS triggers(
  tx_index INTEGER PRIMARY KEY,
  tx_hash TEXT UNIQUE,
  block_index INTEGER,
  source TEXT,
  target_hash TEXT,
  payload BLOB,
  status TEXT,
  FOREIGN KEY (tx_index, tx_hash, block_index) REFERENCES transactions(tx_index, tx_hash, block_index))
```

It is added some indexes.

```
CREATE INDEX IF NOT EXISTS block_index_idx ON broadcasts (block_index)
CREATE INDEX IF NOT EXISTS source_index_idx ON broadcasts (status, source, tx_index)
```

# Compatibility

## backword

This function is independent from other functions. It keeps the backward compatibility.

## forward

This XMPIP doesn't define behaviors of triggered contracts.
They must be defined in XMPIPs depends on this XMPIP.

# Copyright

This document is placed in the public domain.
