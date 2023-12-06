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
d83584616100830001582782815820ee90735ac719e85dc2f3e5974036387fdf478af7d9d1f8480e97eee601890266820001
8158406b71fd0c8ae2ccc910c39dd20e76653fccca2638b7935f2312e954f5dccd71b209c58ca57e9d4fc2d3c06a57d585db
adf4535abb8a9cf103eeb9b9717d87f201 // 117 bytes
```

Diagnostic notation:
```
53([           // cbor tag 53
  "a",         // name: "a", UTF-8 string
  0,           // name's age: 0, unsigned integer
  [            // name's payload, alias service area, array
    0,         // service code: 0, unsigned integer
    1,         // service operation code: 1, unsigned integer
    <<[        // service operation parameters, array, defined by service protocol
      [        // here is ed25519 public keys list, indicates the holder of the Name "a"
        h'ee90735ac719e85dc2f3e5974036387fdf478af7d9d1f8480e97eee601890266'
      ],       // service operation options, here is a tuple of (0, 3)
      [
        0,     // 0 indicates public key' kind is ed25519
        1      // 1 idicates verifing threshold is 1
      ]
    ]>>
  ],
  [            // signatures array
    h'6b71fd0c8ae2ccc910c39dd20e76653fccca2638b7935f2312e954f5dccd71b209c58ca57e9d4fc2d3c06a57d585dbadf4535abb8a9cf103eeb9b9717d87f201'
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
