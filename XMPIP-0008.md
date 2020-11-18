<pre>
  XMPIP: 0008
  Title: Addresses that reject receiving assets with typical flags
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Base CIPs: 12
  Created: 2020-11-18
</pre>

## Abstract ##

Any user should be able to mark an address under their control as rejecting some kind of assets.
Because some dapps can't handle assets that have flags like 'reassignable'.

## Motivation ##

To lessen end user support costs by rejecting to receive assets that have typical flags.

## Rationale ##

XMPIP-0003 allows for specifying flags that can charactarize asset's behavior.
And XMPIP-0004, XMPIP-0005, XMPIP-0006 defines flags that can set to asset.
After these XMPIPs activated, some applications are facing some issues.

Example: Monapachain is the tipbot service receive assets from one user and sends another users.
It can't send a recived assets when the asset is marked as 'un-reassignable'.

The service provider will reduce their service cost if the send contracts invalidate messages with 'un-reassignable' assets. 

The address flag has already standarized by CIP-12.
The issue discussed in this XMPIP will be resolved by some extensions to CIP-12.

## Specification ##

There will be a 'special' broadcast message that is used to set address options; `OPTIONS <INTEGER>`.
(implemented in CIP-12)

## Changes ##

### Flags for OPTIONS

```
ADDRESS_OPTION_REJECT_ASSET_DIVIDIBLE    = 0x000100
ADDRESS_OPTION_REJECT_ASSET_LISTED       = 0x000200
ADDRESS_OPTION_REJECT_ASSET_REASSIGNABLE = 0x000400
ADDRESS_OPTION_REJECT_ASSET_VENDABLE     = 0x000800
ADDRESS_OPTION_REJECT_ASSET_LOCKED       = 0x100000
ADDRESS_OPTION_REJECT_ASSET_UNLOCKED     = 0x200000
```

Note: Applying `ADDRESS_OPTION_REJECT_ASSET_LOCKED | ADDRESS_OPTION_REJECT_ASSET_UNLOCKED` make the address reject all send messages.

### Sends

1. When validating a send attempt (send.validate):
   - Calculating asset flags
   - Check if the destination address has flags for rejection.

### API

### get_addresses

gets addresses from the database, in the same manner as other get_* ops.

## Copyright ##

This document is placed in the public domain.