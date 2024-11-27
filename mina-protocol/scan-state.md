# Scan State

扫描状态是一种数据结构, 它允许将交易 SNARK 的生成从区块生产者解耦到 SNARK 工作者. 

区块生产者不必生成交易 SNARK, 因此无论交易吞吐量如何, 区块生产时间都可以保持恒定. 扫描状态数据结构允许并行生成交易 SNARK 证明, 并由多个相互竞争的 SNARK worker 完成. 

扫描状态由一片完全二叉树森林组成, 其中树中的每个节点都是一个由 SNARK worker 者完成的任务. 扫描状态会定期从一棵树的顶部返回一个单一证明, 以证明树的叶子节点所有交易的正确性. 

区块生产者在他们生成的区块链 SNARK 中包含已发出的账本证明, 该证明既证明链的当前状态有效, 又证明 SNARK 账本中包含的所有交易的有效性. 

因此, 无论交易吞吐量如何, 区块时间都可以保持恒定. 扫描状态能够进行调整以匹配所需的交易吞吐量. 

> 在稳定状态下, 当所有 slots 都被填满且所有需要的证明都已完成时, 每个区块都会生成一个账本证明. 

## Including transactions

在构建一个区块时, 一个区块生产者可以包含最多由"扫描状态常量"定义的最大数量的交易. 区块生产者可以收取任何可用的交易费用, 并通过添加交易给自己支付一个 coinbase 奖励. 他们添加的每一笔交易都会被转换为新的基础任务并添加到扫描状态中. 

对于添加的每一笔交易, 区块生产者必须包含与扫描状态中已存在的一系列任务相对应的等量且已完成的 SNARK 工作. 当添加到扫描状态时, 这些已完成的任务会创建新的合并任务, 但根节点除外, 在根节点的情况下, 证明将作为结果返回. 

区块生产者可以从 SNARK pool(snarketplace)中的可用出价中向任何 SNARK 工作者购买已完成的工作, 而不是自己完成工作.

## Scan state constants 

以下常量决定了扫描状态的结构和行为: 

* transaction_capacity_log_2
* work_delay

`transaction_capacity_log_2`常量定义了一个区块中可以包含的最大交易数量: 

```
max_no_of_transactions = 2^{transaction_capacity_log_2}
```

`work_delay`确保 SNARK worker 有足够的时间完成 SNARK 工作. 如果没有可用的已完成证明, 区块生产者不能包含任何交易. 有了工作延迟, 扫描状态下可能存在的最大树的数量由以下定义: 

```
max_number_of_trees = (transaction_capacity_log_2 + 1) * (work_delay + 1) + 1
```

每个区块中可以包含的最大证明数量由以下定义: 

```
max_number_of_proofs = 2^{transaction\_capacity_log_2 + 1} - 1
```

这些扫描状态约束确保: 

* Only a single proof can be emitted per block
* The merge node to be updated after adding proofs corresponding to its children is always empty.

虽然交易的最大数量可以是固定的, 但这个数量可以根据交易吞吐量动态调整. 因此, 扫描状态可以处理无限的交易吞吐量, 尽管这是以(对数地)增加交易证明延迟为代价的. 

