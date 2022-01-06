<pre>
  XMPIP: 0021
  Title: Counterparty API refinement
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Draft
  Type: Standards
  Created: 2022-01-06
</pre>

## Abstract ##

This article defines to applying API refinement with some breaking changes.

Currently, Counterparty-server provides some API to users.
But it have some issues.

* Some tables is not exposed to `get_{table}` API.
* `get_messages()` doesn't fit a `get_{table}` API style even if the `messages` table is in the DB. 
* Some tables don't have `block_index` but `get_{table}` API doesn't care about them.

## Motivation ##

The purpose of this refinement is to provide the better API that exposes tables in DB to API users.

## Specification ##

* The current `get_messages` is renamed to `get_messages_by_block_index`.
* The new `get_messages` accepts `get_{table}` style arguments and returns `get_{table}` style values.
* `get_mempool` ignores two arguments `start_block` and `end_block`.
* `get_addresses`, `get_blocks` and `get_transactions` is added with the `get_{table}` style.

## Rationale ##

The name `get_messages` conflicts `get_{table}` style `get_messages` API.
At least major Monaparty dApps don't use the current `get_message`.
So it'll be acceptable if a breaking change is done.

There already have an `index_begin`/`index_end` guard on `get_balances`, `get_order_matches`, `get_bet_matches`.
So `get_mempool` should be guarded as same as them.

Other refinements exclude above are for reducing dependencies to `sql` API.
It will reduce dependencies to Counterblock-server. We'll get a way to develop new explorers/cache-server/wallet-interfaces.

## Backward Compatibility ##

Two breaking changes.

`get_messages` API have no backward compatibility.
Users can avoid it with using `get_messages_by_block_index` instead.

The result `get_mempool` with `start_block` and/or `end_block` doesn't contain errors.
But almost users have no pain. (Some test codes might failed.)

## Acknowledements ##

## Copyright ##

This XMPIP is released under CC0-1.0 and therefore Public Domain.
