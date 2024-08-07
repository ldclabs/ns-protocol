# Naming & Service Protocol
🧬 Inscribe trusted naming and service database on the Bitcoin network.

> This document specifies a data structure based on [RFC8949 CBOR][cbor] for carrying next-generation naming and service databases on the Bitcoin network, an decentralized, open,, public, and trusted infrastructure for building the Web3 ecosystem.

## Overview 概述

Naming & Service Protocol，命名和服务协议，简称为 NS 协议。

比特币网络拥有最强最稳固的共识，在原本的金融价值之外，它的 Taproot 脚本机制可以用于铭刻恒久远的价值信息。

本文档定义了一种基于 [RFC8949 CBOR][cbor]标准的数据结构，用于在比特币网络上铭刻下一代命名和服务数据库。这是一种去中心化、完全开放、公开可信的基础设施，用于构建 Web3 生态系统。

任何人都可以在比特币网络上铭刻符合 NS 协议的数据。

任何人都可以运行 NS-Indexer 索引器，从比特币网络上提取这些数据，构建 NS 完整数据状态，为任何其它上层应用提供数据服务。

任何人用 NS-Indexer 索引器构建出来的 NS 数据状态都应该是一致的，它们也是基于区块链结构，有轻量级的验证机制，可以被快速验证。

### 协议介绍

简单来说，NS 协议定义了一种用特币网络承载名称和服务信息的数据库。

Naming Protocol 命名协议可以在比特币网络上创建名为 `name` 的数据库，任何人都能读这个数据库，但只有持有 `name` 的 `public_key` 公钥对应私钥的用户才能往这个数据库写入数据。
Service Protocol 服务协议则定义了数据库的数据模型，只有通过数据模型校验的数据才能被写入。

比如，基于 NS 协议，Alice 可以进行：
1. 基于 `0` 号服务协议，用一组公钥声明对 `alice` 名称的所有权，此后，Alice 用该名称在比特币链上进行其它行为都需要用对应私钥签名，从而可以被任何人用公钥验证，其它任何人也不能冒用该名称。当然，在比特币网络上，声明是先到先得，如果 `alice` 已被他人声明，Alice  就无法声明了。
2. 基于 `1` 号服务协议，添加一组 [RFC9052 COSE][cose] 标准的公钥，区别于 `0` 号服务协议中声明的公钥，这组公钥具备更大的灵活性和扩展性，用于比特币链下的其它互联网行为验证。比如作为 Alice 邮件的验证公钥，作为 Alice 网站的证书公钥等。
3. 基于 `2` 号服务协议添加 Alice 的个人公开信息、社交媒体账号信息等。
4. 基于 `3` 号服务协议添加 Alice 的钱包收款地址，或者网站 IP 信息，其他用户通过 NS-Indexer 可以查询 `alice` 的网站 IP 并访问和验证。

还可以定义其它服务协议，如 NFT、RWA、DAO 等，标准化的服务协议可以更高效地引导服务提供者（即应用）和用户共建 Web3 生态系统。

第一个 NS 铭文可以在 https://mempool.space/tx/8e9d3e0d762c1d2348a2ca046b36f8de001f740c976b09c046ee1f09a8680131 看到，该交易包含了36个 NS 铭文：

![NS-Inscription](./ns-inscription-tx.webp)

### 协议实现

[LDC Labs](ldclabs) 开源了 NS 协议的 Rust 实现，包括：

| Crate                                                                                                                            |                  | Crates.io                                                                                               | Documentation                                                                            |
| -------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| [Generic NS-Protocol library implementation](https://github.com/ldclabs/ns-rs/tree/main/crates/ns-protocol)                      | [`ns-protocol`]  | [![crates.io](https://img.shields.io/crates/v/ns-protocol.svg)](https://crates.io/crates/ns-protocol)   | [![Documentation](https://docs.rs/ns-protocol/badge.svg)](https://docs.rs/ns-protocol)   |
| [NS-Protocol Indexer service in Rust](https://github.com/ldclabs/ns-rs/tree/main/crates/ns-indexer)                              | [`ns-indexer`]   | [![crates.io](https://img.shields.io/crates/v/ns-indexer.svg)](https://crates.io/crates/ns-indexer)     | [![Documentation](https://docs.rs/ns-indexer/badge.svg)](https://docs.rs/ns-indexer)     |
| [NS-Protocol Inscriber library in Rust](https://github.com/ldclabs/ns-rs/tree/main/crates/ns-inscriber)                          | [`ns-inscriber`] | [![crates.io](https://img.shields.io/crates/v/ns-inscriber.svg)](https://crates.io/crates/ns-inscriber) | [![Documentation](https://docs.rs/ns-inscriber/badge.svg)](https://docs.rs/ns-inscriber) |
| [NS-Protocol Inscriptions and states data fetcher library in Rust](https://github.com/ldclabs/ns-rs/tree/main/crates/ns-fetcher) | [`ns-fetcher`]   | [![crates.io](https://img.shields.io/crates/v/ns-fetcher.svg)](https://crates.io/crates/ns-fetcher)     | [![Documentation](https://docs.rs/ns-fetcher/badge.svg)](https://docs.rs/ns-fetcher)     |

### 协议数据预览

NS 协议的数据结构是基于 [RFC8949 CBOR][cbor] 的，CBOR 是一种轻量级且表达能力先进的二进制数据格式，它的数据结构类似 JSON，但是比 JSON 更高效，是 JSON 的超集。NS 协议使用 [53 号 CBOR Tag](./cbor-dns-tag-53.md)（53 是 DNS 服务默认端口）标记数据包。

一个 NS 铭文的数据结构如下：

![NS-Protocol](./ns-protocol.webp)

第一个NS铭文的数据包如下:
```
d8358461300082008182018181582031d6ec328b42051a63c1619afad4e60b78f4991e62337918fe
2d2e694a4f88f7815840be26e66f4054be7793daff1d7489d8c42db596fd3041433ddf75c9396aca
ff764ab6c8ec6f653f2579909313761dad517853a681cb7e3fed73b33844c04ec406 // 114 bytes
```

可视化解析（工具 https://cbor.me）：
```
53([           // cbor tag 53, quadruple in array
  "0",         // name: "a", UTF-8 string
  0,           // name's updating sequence, unsigned integer, 0 is the first sequence
  [            // name's service payload, two-tuples or triple in array
    0,         // service code, unsigned integer, 0 is the native Name service
    [          // updating operations, array
      [        // first operation, two-tuples in array
        1,     // operation subcode
        [      // operation params, struct defined by service protocol in cbor schema
          [    // first param: here is ed25519 public keys list, indicates the holder of the Name "a"
            h'31d6ec328b42051a63c1619afad4e60b78f4991e62337918fe2d2e694a4f88f7'
          ]
        ]
      ]
    ]
  ],
  [            // signatures array
    h'be26e66f4054be7793daff1d7489d8c42db596fd3041433ddf75c9396acaff764ab6c8ec6f653f2579909313761dad517853a681cb7e3fed73b33844c04ec406'
  ]
])
```

这个 NS 铭文数据的 `name` 是 `"0"`，`sequence` 是 `0`，`service` 区服务协议 `code` 是 `0`，包含一个操作，其 `subcode` 是 `0`，`params` 则是公钥数组，包含了一个 Ed25519 公钥 `31d6ec...88f7`，表明该公钥控制了名称 `"0"`。

## Naming Protocol 命名协议

如上图所示，命名协议是一个四元组，由长度为4的 CBOR 数组表示，分别是 `name`、`sequence`、`service` 和 `signatures`：
- `name`：名称，是一个长度为1到64字节的 UTF-8 字符串，但不能是大写字符、标点符号、分隔符、字符标记、符号、控制符等，验证逻辑详见 [NS-Protocol 源码](https://github.com/ldclabs/ns-rs/blob/30db5871b6c0e5706e249c133aa7663c441f3c2d/crates/ns-protocol/src/ns.rs#L138)。
- `sequence`：名称的更新序列号，是一个无符号整数，首次声明名称所有权时的初始值为 `0`，每次更新名称状态都会递增 `1`。
- `service`：名称的服务负载区，详见服务协议。
- `signatures`：数据包签名区，是一个签名数组，签名的内容为 `[name, sequence, service]` 三元组，签名逻辑详见 [NS-Protocol 源码](https://github.com/ldclabs/ns-rs/blob/30db5871b6c0e5706e249c133aa7663c441f3c2d/crates/ns-protocol/src/ns.rs#L201)，所要求的签名数量由 `0` 号服务协议定义。

## Service Protocol 服务协议

如上图所示，服务协议是一个二元组或三元组，由长度为2或3的 CBOR 数组表示，分别是 `code`、`operations` 和可选的 `attesters`：
- `code`：服务协议代号，是一个64位无符号整数，用于标识服务协议的类型。
- `operations`：服务状态数据操作区，是长度至少为1的 `Operation` 数组，一次更新可以包含同一服务下的多个操作。`Operation` 则是一个二元组，由 `subcode` 和 `params` 组成，`subcode` 是操作子码，`params` 是 CBOR 格式的操作参数，由 CBOR Schema（基于 [cbor-typeof-tag]）定义，它们的具体含义由服务协议定义。
- `attesters`：可选的证明人，如果提供，它应该是1到 n 个已声明的 `name`，证明人应该按照命名协议的描述对数据包进行签名，NS-Indexer 索引器会验证该签名。`attesters` 用于需要服务提供方和用户共同确认的可信数据场景。比如数字签证场景下，服务提供方是某机构，用户是某个人，服务提供方和用户都需要对数据包进行签名，才能确认这个签证数据是可信的。

服务协议代号计划如下：
| Code | Name         | Description                                                                                                                                                                         |
| ---- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `0`  | Name Service | 服务于 NS 协议自身的服务协议，用于更新名称的公钥列表                                                                                                                                |
| `1`  | PKI Service  | [计划中] 公开密钥基础设施协议，支持所有 [RFC9052 COSE][cose] 标准的公钥                                                                                                             |
| `2`  | KYC Service  | [计划中] 标准化的公开个人信息和可以零知识证明的半公开个人隐私信息                                                                                                                   |
| `3`  | DNS Service  | [计划中] 能逐步取代现有域名系统的下一代域名服务，NS 协议使用 `:` 分割子域名，比如 Alice 可以用 `alice:web` 作为网站域名，网站完整链接是 `ns:/alice:web`，相当于 `https://web.alice` |

更多其它服务协议将由 NS 社区提案定义。

需要注意的是，往比特币网络写入数据成本较高，并且是公开的，服务方和用户只应该把最核心的、需要公开的数据写入链上。更多业务数据应该由链下的服务提供方和用户自行存储，NS-Indexer 索引器只负责提供链上可信数据的查询验证服务。

比如域名场景下，`name` 实际相当于顶级域名，用户可以把该顶级域名的权威 Nameserver 解析记录到链上，并且可能需要域名服务提供方作为 `attesters` 证明人，子域名相关信息无需上链，其解析则由域名服务提供方负责。这样，用户可以通过 NS-Indexer 索引器查询到域名的权威 Nameserver，然后再到权威 Nameserver 查询子域名的解析记录，并且子域名解析记录也是由 `1` 号 PKI 服务的公钥来验证的。用户对域名拥有绝对控制权，不但可以彻底解决域名劫持问题，还可以实现域名的去中心化，域名服务提供方也可以是多个，用户可以自由选择。

### `0` 号服务协议：Naming Service

`0`号服务协议是服务于 NS 自身的协议，它定义了名称的所有权声明和更新操作，以及名称状态数据的验证。

| Subcode | Params            | Description                                    |
| ------- | ----------------- | ---------------------------------------------- |
| `0`     | `null`            | 最轻量的续约操作，仅更新 sequence 和状态时间   |
| `1`     | `PublicKeyParams` | 确认新的公钥数组，即声明所有权                 |
| `2`     | `PublicKeyParams` | 声明新的公钥数组，即允许控制权转移到这组新公钥 |

**`0`号操作及名称所有权有效期：**

虽然比特币网络铭文记录的数据是永久了，但 NS 协议定义的名称所有权并不是永久的，协议定义了名称失效时间 `NAME_STALE_SECONDS` 为365天，过期时间 `NAME_EXPIRE_SECONDS` 则为 `NAME_STALE_SECONDS` 之后的180天。

如果用户的名称在365天内没有更新操作，则处于失效状态——NS-Indexer 索引器对该名称的数据验证将标记为失败，如果再过180天还是没有任何更新操作，则该名称过期，其他用户可以声明该名称的所有权。

用户对名称的任何更新操作都会延续名称的有效期，最轻量的操作就是 `0` 号服务协议下的 `0` 号操作。

注意，时间以比特币区块的 `block_time` 为准。

**`PublicKeyParams` 定义：**

在 Rust 源码中定义如下：
```rs
pub struct PublicKeyParams {
    pub public_keys: Vec<Vec<u8>>,
    pub threshold: Option<u8>, // default to public_keys.len()
    pub kind: Option<u8>,      // default to 0: ed25519
}
```

用 CBOR 描述则为一元、二元或三元组：
+ `public_keys`：公钥数组，每个公钥是一个字节数组，建议不超过5个公钥，多签建议使用3或4个公钥。
+ `threshold`：可选的多签阈值声明，如果提供则必须小于等于 `public_keys` 的长度，默认为 `public_keys` 的长度。
+ `kind`：可选的公钥类型声明，目前只支持 `0` 号 `ed25519` 公钥。

NS 协议有4种阈值验证 [ThresholdLevel](https://github.com/ldclabs/ns-rs/blob/30db5871b6c0e5706e249c133aa7663c441f3c2d/crates/ns-protocol/src/ns.rs#L62)，分别是：
- Single - 单签名验证，只要有一个签名验证成功即通过，使用场景如：确认某用户是名称持有者之一。
- Default - 默认多签验证，需要有 `threshold` 数量的签名验证成功才通过，一般名称状态更新使用该验证，`0-0` 号操作使用该值验证。
- Strict - 严格多签验证，需要有 `threshold + 1`（但不超过公钥数量）数量的签名验证成功才通过，`0-2` 号操作使用该值验证。
- All - 全签名，需要全部公钥签名验证成功才通过，`0-1` 号操作使用该值验证。需要注意的是，这里的全部公钥是新所有者的全部公钥。

比如名称 `"x"` 声明了4个公钥，阈值为2，那么：
- 某些服务提供方只需要单签名验证，任何1/4公钥签名验证成功即可；
- `"x"` 更新其它服务协议时，NS-Indexer 会使用默认多签验证，需要2/4公钥签名才验证成功；
- `"x"` 要更新公钥数组（如转移所有权）发起 `0-2` 号操作时，NS-Indexer 会使用严格多签验证，需要3/4个公钥签名才验证成功；
- `"x"` 持有人声明所有权 `0-1` 号操作，NS-Indexer 会使用全签名验证，需要4个公钥签名才验证成功。

又比如名称托管场景下，最合适的设置是3个公钥，其中用户持有2个公钥，托管商持有1个公钥，阈值为1，那么：
- 用户或者托管商能发起任何常规更新操作，如名称续期；
- 需要转移所有权时，就必须有用户参与了，托管商单方面无法声明新的公钥数组；
- 即使某一个公钥丢失了，用户可以利用还有效的两个公钥签发 `0-2` 号操作声明新公钥数组，再用全部新公钥签发 `0-1` 号操作确认新公钥数组，这两个操作可以在一个铭文请求中完成。

除了 `threshold` 阈值机制，NS 协议也天然支持 [FROST](frost) 阈值签名验证，FROST 生成多方签名的流程相对复杂。

## NS-Indexer State

通过 NS 协议往比特币网络写入的数据相当于数据库写入日志，它更新了数据库的状态。数据库状态需要通过 NS-Indexer 索引器构建出来，从而为上层应用提供可信数据服务。
任何人运行 NS-Indexer 索引器构建出来的 NS 数据状态都应该是一致的，NS 数据状态本身也是基于区块链结构，可以被快速校验。

Rust 源码：
https://github.com/ldclabs/ns-rs/blob/main/crates/ns-protocol/src/state.rs

[NS-Fetcher](ns-fetcher) 是用 Rust 实现的一个轻量级的同步和验证 NS 数据状态的库。不希望运行完整 NS-Indexer 节点的用户可以使用该库，它可以从任何一个完整 NS-Indexer 节点同步数据，然后验证数据状态。

[cbor]: https://datatracker.ietf.org/doc/html/rfc8949
[cose]: https://datatracker.ietf.org/doc/html/rfc9052
[cbor-typeof-tag]: https://github.com/cbor-schema/cbor-typeof-tag
[frost]: https://github.com/ZcashFoundation/frost
[ldclabs]: https://github.com/ldclabs
[ns-fetcher]: https://github.com/ldclabs/ns-rs/tree/main/crates/ns-fetcher
