## Introduction

Mina 协议是一个一层协议, 旨在实现区块链, 真正去中心化, 可扩展性和安全性的最初承诺. 

Mina 提供了一个优雅的解决方案: 使用易于验证, 大小一致的加密证明取代区块链. Mina 大大减少了每个用户需要下载的数据量. 区块链参与者不是从一开始就验证整个链, 而是使用递归零知识证明(zk-SNARKs)完全验证网络和交易. 然后, 节点可以存储较小的证明, 而不是整个链. 因为它的大小一致, 所以即使 Mina 扩展到许多用户并积累了多年的交易数据, 它同样能保持可访问性. 

### The Mina Protocol​ 

有三个公共 Mina 协议网络：

1. mainnet - the production network mainnet
2. devnet - the test network based on the same software versions as the Mainnet
3. berkeley - a development network where new features are trialed

我们可以使用此 graphQL 查询检查网络: 
```
query MyQuery {
  networkID
}
```

本节介绍 Mina 协议的工作原理. 

* [Proof Of Stake](./proof-of-stake.md)
* [What's in a Block](./whats-in-a-block.md)
* [Block Producers](./block-producers.md)
* [SNARK Workers](./snark-workers.md) 
* [Scan State](./scan-state.md)
* [Time-Locked Accounts](./time-locked-accounts.md) 
* [Sending a Payment](./sending-a-payment.md)
* [Lifecycle of a Payment](./lifecycle-of-a-payment.md)
