<pre>
  XMPIP: 0013
  Title: IPFS support
  Authors: Cryptcoin Junkey
  Discussions-To: 
  Status: Implemented
  Type: Standards
  Created: 2020-11-16
</pre>

## Abstract ##

Counterblock server handles some external information from predefined JSON files.
But they can't have any persistence guaranty. the IPFS support solves this by accepting `ipfs://` URLs.
Contents in IPFS is get persisitency by explicit "pin".
But in this XMPIP, it is optional whether the counterblock server make a request for pinning to IPFS server.
And also it is optional whether the federated-node have IPFS node.

## Motivation ##

Counterblock server handles some external information from predefined JSON files.
It is also got images from the location described in JSON files.
Their location is assumed on the web.
The issue is "contents on the web have no parsistence guaranty". 
Some contents may be deleted after the counterblock server paesed.
Such contents will be lost if the server was reparsed.
Contents should have persistency at least until all Monaparty node have been lost.

## Rationale ##

IPFS (InterPranetary File System) is a P2P storage protocol and server implemantations.
an IPFS node can bind any contents (they call this bind as "pin").
And all IPFS nodes can query to another nodes and can collect contents from content holders.
As the network is P2P, all IPFS node is both of server and client.
It can be apply persitence to all contents that used in Counterblock, by "pin"ning to IPFS network.


## Technical implementation ##

* Counterblock MUST accept `ipfs://{cid}` on places thet `https://` and `http://` can be accepted.
* Counterblock MUST ignore filename suffixes (like `.json`, `.png`) in URL if its scheme is `ipfs`
* Counterblock MUST communicate with IPFS node and store required information.
* Counterblock MAY make a request for pinning the content to IPFS node.
* Federated-node MAY have IPFS server to keep contents.

## Legal notice ##

Note that this XMPIP doesn't require all Counterblock nodes have IPFS node.
In some countries, there may have some legal risks running P2P filestore with blockchain.

e.g. When a link to the illegal image is deployed to the chain, IPFS node will pin the image.
It cannot be denied the possibility of being subject to the law by simple possession.

# API Changes

None.

# Crowdfunding

IPFS asset is dispending on Monacoin address MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu

https://mpchain.info/tx/111901

# Copyright

This document is placed in the public domain.
