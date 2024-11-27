# Introduction 

[Mina Protocol](../index.md) 是一个由 [proof of stake consensus](../proof-of-stake.md) 保障安全的一层区块链. 

Mina 协议有三个公开网络: 

1. `mainnet`  - the production network
2. `devnet`   - the test network based on the same software versions as the mainnet
3. `izmir`    - a development network where new features are trialed

你可以通过以下 GraphQL 查询来检查网络的标识: 

```
query MyQuery {
  networkID
}
```

## Node Operators

节点运营者是在 Mina 网络上运行 Mina 节点的网络参与者. 

本节描述了在 Mina 上的操作以及节点运营者如何运行 Mina 节点, 相关内容包含以下部分: 

- [Staking and Snarking](/node-operators/staking-and-snarking) - How to stake your MINA
- [Generating a Key Pair](/node-operators/generating-a-keypair) - How to generate key pairs
- [Block Producing Node](/node-operators/block-producer-node) - Running a Block Producer
- [SNARK Workers](/node-operators/snark-workers) - Running a SNARK worker
- [Archive Nodes](/node-operators/archive-node) - Running an Archive Node
- [Seed Peers](/node-operators/seed-peers) - Running a Seed Peer
- [Data and History](/node-operators/data-and-history) - A look at retrieving historic data.
- [Delegation Program](/node-operators/delegation-program) - The Mina Foundation Delegation Prgram for Block Producers 
- [Staking Service Guidelines](/node-operators/staking-service-guidelines)
- [Mina Signer](/node-operators/mina-signer)
- [Mina CLI Reference](/node-operators/mina-cli-reference) - Guide to CLI interactions with Mina networks 
- [Troubleshooting](/node-operators/troubleshooting)
- [FAQ](/node-operators/faq)

## Mina Nodes

节点是运行 Mina 守护进程的机器. 不同的节点在 Mina 网络中扮演着不同的角色: 

1. [Block Producer](https://docs.minaprotocol.com/glossary#block-producer) - A node that participates in a process to determine what blocks it is allowed to produce and then produces blocks containing transactions that can be broadcast to the network. People who run [block producer](https://docs.minaprotocol.com/mina-protocol/block-producers) nodes are also called block producers.

2. [SNARK Coordinators](https://docs.minaprotocol.com/glossary#snark-coordinator) - A role on a Mina node in the Mina network that distributes work to a series of SNARK workers in parallel to block production.

3. [SNARK Workers](https://docs.minaprotocol.com/glossary#snark-worker) -  SNARK workers create [zk-SNARKs](https://minaprotocol.com/blog/what-are-zk-snarks) for each transaction. These zk-SNARKs are used to create recursive zk-SNARKs that prove the correctness of a block, and in turn, these zk-SNARKs are used to create recursive zk-SNARKs that prove the correctness of the network. These zk-SNARKs help provide the Mina Protocol with succinctness. 

4. [Archive Nodes](/node-operators/archive-node) - A regular mina daemon that is connected to a running `mina-archive` process. The daemon regularly sends blockchain data to the archive process that stores it in a [PostgreSQL](https://www.postgresql.org/) database.

5. [Seed Nodes](https://docs.minaprotocol.com/glossary#seed-nodes) - Keep a record of nodes in the network and enable nodes joining the network to connect to peer nodes.

## Mina Protocol

[Mina Protocol](../index.md) 描述了 Mina 协议的工作原理. 

## Node Developers

[Node Developers](../node-developers/index.md) 描述了开发者如何对 Mina 节点进行添加功能和改进. 

## Exchange Operators

[Exchange Operators](../exchange-operators/faq.md) 描述了如何连接到 Mina 网络并提供对 Mina 代币的访问权限.  