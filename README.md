# Name & Service Protocol

This document specifies a data structure based on [RFC8949 CBOR][cbor] for carrying next-generation Name and Service information, which is a decentralized, public, and trusted infrastructure for building the Web3 ecosystem.

## Overview

The protocol specifies a new [IANA: Cbor Tag][Iana-CBor-tags] `53` (in homage to Port 53 of the DNS).

```
Tag: 53
Data item: Name & Service Protocol
Semantics: Describes the struct of Name & Service data
Point of contact: Yan Qing <txr1883@gmail.com>
Description of semantics: https://github.com/ldclabs/ns-protocol
```

A simple data sample is as follows:

Hex:
```
d83584616100820081820182815820ee90735ac719e85dc2f3e5974036387fdf478af7d9d1f8480e
97eee60189026601815840e23554d996647e86f69115d04515398cc7463062d2683b099371360e93
fa1cba02351492b70ef31037baa7780053bcf20b12bafe9531ee17fe140b93082a3f0c // 115 bytes
```

Diagnostic notation:
```
53([           // cbor tag 53, quadruple in array
  "a",         // name: "a", UTF-8 string
  0,           // name's updating sequence, unsigned integer, 0 is the first sequence
  [            // name's payload, alias service area, two-tuples or triple in array
    0,         // service code, unsigned integer, 0 is the native Name service
    [          // updating operations, array
      [        // first operation, two-tuples in array
        1,     // operation code
        [      // operation params, struct defined by service protocol in cbor schema
          [    // first param: here is ed25519 public keys list, indicates the holder of the Name "a"
            h'ee90735ac719e85dc2f3e5974036387fdf478af7d9d1f8480e97eee601890266'
          ],
          1    // second param: idicates verifing threshold is 1
        ]
      ]
    ]
  ],
  [            // signatures array
    h'e23554d996647e86f69115d04515398cc7463062d2683b099371360e93fa1cba02351492b70ef31037baa7780053bcf20b12bafe9531ee17fe140b93082a3f0c'
  ]
])
```

The data indicates that a holder with the public key `ee90735a... 01890266` holds the Name `a`, which should be used to verify trust when the holder publishes signed data as `a`.

## Name Protocol
**ToDO**

## Service Protocol
**ToDO**

[cbor]: https://datatracker.ietf.org/doc/html/rfc8949
[iana-cbor-tags]: https://www.iana.org/assignments/cbor-tags/cbor-tags.xhtml
