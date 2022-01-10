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
Such stores are enough if they provide BLOB. Each contents will be parsed in code.

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


## Backward Compatibility ##

None.

## Acknowledements ##

## Copyright ##

This XMPIP is released under CC0-1.0 and therefore Public Domain.
