# CHIP-2023-05 URI Scheme Extensions for CashTokens

        Title: URI Scheme Extensions for CashTokens
        First Submission Date: 2023-05-30
        Owners: Jonas Lundqvist
        Type: Technical
        Layers: Applications
        Status: Withdrawn
        Current Version: 0.1.0
        Last Edit Date: 2023-05-30

## Contents

<details><summary><strong>Table of Contents</strong></summary>

- [Summary](#summary)
- [Deployment](#deployment)
- [Motivation](#motivation)
- [Benefits](#benefits)
- [Technical Description](#technical-description)
- [Specification](#specification)
- [Security Analysis](#security-analysis)
- [Implementations](#implementations)
- [Test Cases](#test-cases)
- [Activation Costs](#activation-costs)
- [Ongoing Costs](#ongoing-costs)
- [Costs and Benefits Summary](#costs-and-benefits-summary)
- [Risk Assessment](#risk-assessment)
- [Evaluation of Alternatives](#evaluation-of-alternatives)
- [Discussions](#discussions)
- [Statements](#statements)
- [Acknowledgements](#acknowledgements)
- [Changelog](#changelog)
- [Copyright](#copyright)

</details>

## Summary

[[Back to Contents](#contents)]

We propose an URI scheme extensions for handling CashTokens.
This is the minimum needed for a wallet or service to request a specific amount and/or commitment of a specific token category. Wallets that are not CashTokens aware only needs to follow the [BIP-21][1] requirement of ignoring unknown query parameters to be compliant with this CHIP.

## Deployment

[[Back to Contents](#contents)]

Every wallet and service can deploy these changes independantly. However, the full benefits of this scheme is availible when a critical mass of wallets and services has implemented the specification in this CHIP.

## Motivation

[[Back to Contents](#contents)]

There is a need to be able to easily share a QR code or URI string for requesting specific tokens and amounts of such. By just exposing an empty address a user is expected to manually specify the relevant token(s) and amount(s) which is cumbersome and error prone.

## Benefits

[[Back to Contents](#contents)]

By letting the wallet software handle token selection and amounts precisely in the way the requestor has specified common user errors is avoided.

## Technical Description

[[Back to Contents](#contents)]

Query parameters that are appended to a [BIP-21][1] URI will specify which token category(ies), NFT commitment(s) and/or FT amount(s) that are required to fulfill a payment request.

## Specification

[[Back to Contents](#contents)]

### v0/v1 Addresses

A query parameter `t` can optionally be added on cashaddresses of version 0 or 1 to signal that the receiver is using a CashToken aware wallet. Wallets that do not have knowledge of this parameter should, according to [BIP-21][1], ignore it. Wallets shall not send any CashTokens to v0/v1 addresses that lacks the `t` parameter.

### v2/v3 Addresses

A query parameter `tid` with a value of a CashToken category as a hexstring can be added to a cashaddress of version 2 or 3 for requesting a specific CashToken category. The next parameter can optionally be `tft` with an integer value of of fungible tokens in the previously specified category.
The parameter could also be `tnft` with one or several comma separated hex strings for NFTs in the same category.

Multiple `tid` could be specified in one URI and any subsequent`tft` and/or `tnft` belongs to that specific token category up until another `tid` parameter.

Any `amount` parameter will still be interpreted as pure BCH amounts as specified in [BIP-21][1].

Any `tid`, `tft` or `tnft` parameter appended to a cashaddress v0 or v1 should be disregarded.

### Examples

| URI  | Description | Notes |
|------|-------------|-------|
| `bitcoincash:qzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzqg6xzf233` | v0 address without parameters | A sending wallet SHOULD NOT send CashTokens to this address |
| `bitcoincash:qzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzqg6xzf233?t` | v0 address with `t` parameter | A sending wallet can send any CashToken to this address |
| `bitcoincash:zzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzq0s4u8vwz` | v2 address without parameters |A sending wallet can send any CashToken or BCH to this address |
| `bitcoincash:zzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzq0s4u8vwz?tid=7d1c2777ae9588a4f5a69afe55dc635c96692a5232e915db35f48c9f77aa16ef&tft=123` | v2 address requesting fungible tokens | A sending wallet MUST send `123` fungible tokens of the category `7d1c2777ae9588a4f5a69afe55dc635c96692a5232e915db35f48c9f77aa16ef` to this address |
| `bitcoincash:zzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzq0s4u8vwz?tid=c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db&tnft=1234abcd` | v2 address requesting a non-fungible token | A sending wallet MUST send an NFT with the commitment `1234abcd` category `c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db` to this address |
| `bitcoincash:zzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzq0s4u8vwz?tid=c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db&tnft=1234abcd&tnft=1122eeff9988aabb` | v2 address requesting a non-fungible token and fungible tokens | A sending wallet MUST send an NFT with the commitment `1234abcd` and another with commitment `1122eeff9988aabb` , both of category `c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db` to this address |
| `bitcoincash:zzmxju6j4nas7tq9xqxtqfaxrj2mmvrdzq0s4u8vwz?tid=c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db&tnft=1234abcd&tnft=1122eeff9988aabb&tid=7d1c2777ae9588a4f5a69afe55dc635c96692a5232e915db35f48c9f77aa16ef&tft=123&amount=1.2` | v2 address requesting a non-fungible tokens, fungible tokens and BCH | A sending wallet MUST send an NFT with the commitment `1234abcd` and another with commitment `1122eeff9988aabb` , both of category `c98a3d3a24df5da5d36365f44c56c531ea105b961724f2c9718c891ea11832db` and `123` FTs of the category `7d1c2777ae9588a4f5a69afe55dc635c96692a5232e915db35f48c9f77aa16ef` to this address along with 1.2 BCH |

## Security Analysis

[[Back to Contents](#contents)]

## Implementations

[[Back to Contents](#contents)]

## Test Cases

[[Back to Contents](#contents)]

## Activation Costs

[[Back to Contents](#contents)]

## Ongoing Costs

[[Back to Contents](#contents)]

## Costs and Benefits Summary

[[Back to Contents](#contents)]

A minority of the Bitcoin Cash wallets does **not** ignore unknown query parameters today and will fail to parse any v0/v1 address with the added `t` parameter. Changing the software for those wallets is considered a minor change.

## Risk Assessment

[[Back to Contents](#contents)]

## Evaluation of Alternatives

[[Back to Contents](#contents)]

More advanced payment options is deferred to extensions of better suited payment protocols, such as [BIP-70][2] and/or [jsonPaymentProtocol][3].
For instance, this CHIP does not support a pay-to-many requests or combinations of optional and mandatory token categories.

Both [BIP-70][2] and [jsonPaymentProtocol][3] demands more effort to implement and thus the trivial use-cases as handled by this CHIP should be more easily implemented.

## Discussions

[[Back to Contents](#contents)]

## Statements

[[Back to Contents](#contents)]

## Acknowledgements

[[Back to Contents](#contents)]

## Changelog

[[Back to Contents](#contents)]

| Version | Date | Changes |
|---------|------|---------|
|0.1.0    | 2023-05-30 | Initial draft |
|0.2.0    | 2023-06-03 | Withdrawn     |

## References

[1]: https://github.com/bitcoin/bips/blob/master/bip-0021.mediawiki
[2]: https://github.com/bitcoin/bips/blob/master/bip-0070.mediawiki
[3]: https://github.com/bitpay/jsonPaymentProtocol/blob/master/v2/specification.md

## Copyright

[[Back to Contents](#contents)]

To the extent possible under law, this work has waived all copyright and related or neighboring rights to this work under [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

