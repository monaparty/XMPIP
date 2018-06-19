---
XMPIP: 2
Title: Monaparty Payment URI Scheme
Author: Cryptcoin Junkey <cryptcoin.junkey@gmail.com>
Discussions-To: 
Status: Accepted
Type: Informational
Created: 2018-06-20
---

This XMPIP is based off of [Counterparty CIP 2](https://github.com/CounterpartyXCP/cips/edit/master/cip-0002.md) by Devon Weller.  This proposal extends CIP 2 to support specifying Monaparty assets.


## Abstract ##
This XMPIP proposes a URI scheme for making payments with Monparty assets and Monacoin.


## Motivation ##

The purpose of this URI scheme is to enable users to easily make payments by simply clicking links on webpages or scanning QR Codes.


## Rationale ##

[Counterparty CIP 2](https://github.com/CounterpartyXCP/cips/edit/master/cip-0002.md) is a widely used payment URI scheme for Bitcoin and Counterparty wallets.  Monaparty should follow this same best practice and modify while extending the protocol to support specifying asset names.


## Specification ##

### General rules for handling (important!) ###

Monaparty clients MUST NOT act on URIs without getting the user's authorization.
They SHOULD require the user to manually approve each payment individually, though in some cases they MAY allow the user to automatically make this decision.

### Operating system integration ###
Graphical Monaparty clients SHOULD register themselves as the handler for the "monaparty:" URI scheme by default, if no other handler is already registered. If there is already a registered handler, they MAY prompt the user to change it once when they first run the client.

### General Format ###

Monaparty URIs follow the general format for URIs as set forth in RFC 3986. The path component consists of a monacoin address, and the query component provides additional payment options.

Elements of the query component may contain characters outside the valid range. These must first be encoded according to UTF-8, and then each octet of the corresponding UTF-8 sequence must be percent-encoded as described in RFC 3986.

### ABNF grammar ###

(See also [a simpler representation of syntax](#simpler-syntax))

    monapartyurn = "monaparty:" monacoinaddress [ "?" monacoinparams ]
    monacoinaddress  = *base58
    monacoinparams   = monacoinparam [ "&" monacoinparams ]
    monacoinparam    = [ amountparam / assetparam / labelparam / messageparam / otherparam / reqparam ]
    amountparam     = "amount=" *digit [ "." *digit ]
    assetparam      = "asset=" *assetname
    labelparam      = "label=" *qchar
    messageparam    = "message=" *qchar
    otherparam      = qchar *qchar [ "=" *qchar ]
    reqparam        = "req-" qchar *qchar [ "=" *qchar ]

Here, "qchar" corresponds to valid characters of an RFC 3986 URI query component, excluding the "=" and "&" characters, which this XMPIP takes as separators.

Here, "assetname" corresponds to a valid Monaparty asset name.  See [Transfer asset](#transfer-asset).

The scheme component ("monaparty:") is case-insensitive, and implementations must accept any combination of uppercase and lowercase letters. The rest of the URI is case-sensitive, including the query parameter keys.

### Query keys ###

* `monacoinaddress`: A monacoin address
* `amountparam`: amount of asset to send [see below](#transfer-amountsize)
* `assetparam`: the type of asset to send [see below](#transfer-asset)
* `label`: Label for that address (e.g. name of receiver)
* `message`: message that describes the transaction to the user ([see examples below](#examples))
* `(others)`: optional, for future extensions

### Transfer amount/size ###

If an amount is provided, it MUST be specified in decimal form and not in watanabes.
All amounts MUST contain no commas and use a period (.) as the separating character to separate whole numbers and decimal fractions.
e.g. `amount=50.00` or `amount=50` is treated as 50. `amount=50,000.00` is invalid.

Monaparty clients MAY display the amount in any format that is not intended to deceive the user.
They SHOULD choose a format that is foremost least confusing, and only after that most reasonable given the amount requested.
For example, so long as the majority of users work in MONA units, values should always be displayed in MONA by default, even if mMONA would otherwise be a more logical interpretation of the amount.

### Transfer asset ###

Valid Monaparty asset names are strings of 4 to 12 uppercase Latin characters (inclusive) not beginning with 'A', or integers between 26^12 + 1 and 256^8 (inclusive), prefixed with 'A'.  `MONA` and `XMP` are also valid Counterparty assets.

If the URI does not specify an asset, the Counterparty client SHOULD prompt the user to specify the asset they wish to send.



## Forward compatibility ##
Variables which are prefixed with a req- are considered required.  If a client does not implement any variables which are prefixed with req-, it MUST consider the entire URI invalid.  Any other variables which are not implemented, but which are not prefixed with a req-, can be safely ignored.



##  Appendix  ##

### Simpler syntax ###

This section is non-normative and does not cover all possible syntax.
Please see the BNF grammar above for the normative syntax.

[foo] means optional, &lt;bar&gt; are placeholders

    monaparty:<address>[?amount=<amount>&asset=<asset>&label=<label>&message=<message>]

### Examples ###

Just the address:

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu

Address with name:

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?label=Junkey

Request payment of 1.3 MONA to "Junkey":

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?amount=1.3&asset=MONA&label=Junkey

Request payment of 20 SOUP to "Junkey":

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?amount=20&asset=SOUP&label=Junkey

Request 5 XMP with a message:

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?amount=5&asset=XMP&label=Junkey&message=Donation+for+all+your+hard+work

Some future version that has variables which are (currently) not understood and required and thus invalid:

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?req-somethingyoudontunderstand=50&req-somethingelseyoudontget=999

Some future version that has variables which are (currently) not understood but not required and thus valid:

    monaparty:MUqM2tDnZXtJ4h87W2g8fFz9nW3GsYhMfu?somethingyoudontunderstand=50&somethingelseyoudontget=999

Characters must be URI encoded properly.



## Copyright ##

This document is placed in the public domain.



## References ##

[Counterparty CIP 2](https://github.com/CounterpartyXCP/cips/edit/master/cip-0002.md) by Devon Weller
