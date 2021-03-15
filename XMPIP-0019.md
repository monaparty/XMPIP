<pre>
  XMPIP: 0019
  Title: Asset Metadata Object
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Created: 2021-03-15
</pre>

## Abstract ##

This article defines Metadata Object bound to Monaparty assets.

Currently, Counterblock supports `extended_asset_info`.
But it have some issues.

* Just an off-chain solution, less persistency even if Monaparty supports IPFS.
* Not easy to handle by users. It's hard to see when Counterblock rejected JSON.

Metadata Object is a kind of key-value-store.
It enables to set multiple metadata by Asset Owners.
Each metadata can be overritten, can be locked, can't be unlocked.

This contract requires some XMP assets as an anti-spam fee.

It will be used in services like Crypto-art, Crypto-music, DNS service, and so on.