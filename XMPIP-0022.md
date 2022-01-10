<pre>
  XMPIP: 0022
  Title: binary store
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Created: 2022-01-10
</pre>

## Abstract ##

This article defines the new message 'binstore'.


## Motivation ##

An upcoming feature "Monaparty Automation" requires a storage for message templates and scripts.
A simple binary storage is enough for such requirements.

In addition , the pure binary storage provides some more values to Monacoin chain as a store of value (SoV).

## Specification ##

The new table `blobstores` is created in the DB.

```
CREATE TABLE blobstores (
    block_index INTEGER,
    tx_index INTEGER,
    tx_hash STRING,
    binary BLOB
-- TODO: TBD more fielids
)
```

The new `get_binstore` is added as a `get_{table}` style counterparty API.
blob is converted to hexified string and set on `binary_hex`.

The new `create_binstore` is added to the counterparty API.
The argument for the `binary` field should be a hexified string and set as `binry_hex`.

valid messages are required an anti-spam fee as they use the DB storage.

```
fee := floor(len(blob) / 1000)
```

The fee is consumed in the last step before the `valid` message is confirmed.
The message will be marked as `invalid` if the XMP balence of the message source is less than the fee.

fee won't consumed if the message status is `invalid`.

## Rationale ##

New APIs don't provide UTF-8 conversion as they are for binaries.

We are not care about huge binaries.
The cracker will be billed many MONA as the miner fee for Such binaries.
So there is no worry even if the fee rate is linear.

At the first plan, we call this function `blobstore`.
But we renamed to `binstore` as it might support a large object operation API in the future.


## Backward Compatibility ##

None.

## Acknowledements ##

## Copyright ##

This XMPIP is released under CC0-1.0 and therefore Public Domain.
