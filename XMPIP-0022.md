<pre>
  XMPIP: 0022
  Title: BLOB store
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Created: 2022-01-10
</pre>

## Abstract ##

This article defines the new message 'blobstore'.


## Motivation ##

An upcoming feature "Monaparty Automation" requires a storage for messages and scripts.
A simple BLOB is enough ro such requirements.

In addition , the pure BLOB storage provides to Monacoin chain as a store of value (SoV).

## Specification ##

The new table `blobstores` is created in the DB.

```
CREATE TABLE blobstores (
    block_index INTEGER,
    tx_index INTEGER,
    tx_hash STRING,
    blob BLOB
-- TODO: TBD more fielids
)
```

The new `get_blobstore` is added as a `get_{table}` style counterparty API.
blob is converted to hexified string and set on `blob_hex`.

The new `create_blobstore` is added to the counterparty API.
The argument for BLOB should be a hexified string and set as `blob_hex`.

valid messages are required an anti-spam fee as they use the DB storage.

```
fee := floor(len(blob) / 1000)
```

The fee is consumed in the last step before the `valid` message is confirmed.
The message will be marked as `invalid` if the XMP balence of the message source is less than the fee.

fee won't consumed if the message status is `invalid`.

## Rationale ##

New APIs don't provide UTF-8 conversion as they are for BLOB.

We are not care about huge BLOBs.
The cracker will be billed many MONA as the miner fee for Such BLOBs.
So there is no worry even if the fee rate is linear.

## Backward Compatibility ##

None.

## Acknowledements ##

## Copyright ##

This XMPIP is released under CC0-1.0 and therefore Public Domain.
