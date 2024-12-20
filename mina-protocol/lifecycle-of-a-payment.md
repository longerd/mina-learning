# Lifecycle of a Payment

在 Mina 中, payments 在被视为 verified and complete 之前要经过几个步骤.

## Gossip Protocol 

Mina 使用 Gossip Protocol 来确保消息能够及时可靠地传输给网络中的所有其他成员. 

## Payments

Payments 是一种交易类型, 要求将价值从一个账户转移到另一个账户, 以及发送方愿意为使支付成功而支付的相关交易费. 

在下面场景中, Bob 想给 Alice 发送一些 MINA. 

1. 要创建一笔付款, Bob 点击发送

    网络中的任何成员都可以创建一笔支付并与 Mina 网络共享. 该支付使用私钥进行加密签名, 以便可以验证发送者的账户. 然后, 该支付被发送给网络中的对等节点进行处理. 当支付被一个对等节点接收时, 它存在于该节点的本地交易池中, 这是该对等节点在网络上监听到的所有交易的 in-memory store.

2. 要生产一个区块, Bob 的付款被放入一个 todo list

    在网络上为给定的时间段选择一个区块生产者节点. 当前活跃的生产者根据支付费用选择正在进行中的支付, 并将它们放入一个待处理的列表中, 称为 transition block. 区块生产者通过构建这些区块赚取 mina. 生产者生成一个 SNARK, 它定义了 transition block 与前一个区块相比的结构(但尚未验证这些新支付). 生产者传输这个新信息以供 SNARK worker 处理. 

3. 为了证明一个 SNARK 交易, Bob 的付款会被 SNARK 签名

    网络上的 SNARK worker 节点开始对 new transition block 的每一步进行 SNARK 计算. 这些是每笔支付的单独证明, 然后合并相邻支付的证明. 最终, 所有支付都得到验证. SNARK worker 可以通过生成这些证明来赚取货币, 由区块生产者从他们的区块奖励中支付. 这些证明通过网络传输出去. 

4. 为了验证付款, Alice 和Bob 的账户显示转账结果

    在整个区块得到验证后, 区块生产者发出 transition block 的确认. 然后网络上的成员节点将这些更改应用于它们的本地账户余额, 以反映这些变化. 

5. 为了达到支付 confidence level, Alice 确信转账已完成

    随着每个后续区块的产生, 接收方对支付实际上已完成以及网络对该区块已达成共识的 confidence 会更高. 然而, 与大多数区块链一样, 在经过一定数量的区块后, 支付被认为是已确认的, 这也被称为交易 finality. 



## Failure Scenarios 

### Transaction is not accepted by the network 

与对等节点共享的交易可能不被接受的几个原因: 

* 该交易是无效的. 例如, 发送方的账户不存在、账户没有足够的资金、签名与账户不匹配或者交易中的随机数没有递增. 

* 网络中可能存在恶意节点拒绝为网络中的特定发送者提供服务. 然而, 这种行为会受到极大的抑制, 并且只要有一个诚实的节点就足以防止这个问题. 

### Transaction is not included in a block 

如果一笔交易是有效的, 并且网络是诚实的, 那么在所有可能性中, 一笔交易很可能会被纳入一个区块. 然而, 有一种情况, 一笔交易可能会从交易池中被移除. 

如果交易池达到其容量, 即`max_txpool_size`, 那么它会移除池中费用最低的交易, 使其从内存中被丢弃. 如果发生这种情况, 发送方需要根据当时的市场动态重新发送具有更高交易费的交易. 

