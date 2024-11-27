# Sending a Payment

## How to send a MINA payment using the Mina CLI.

> 注意, 本节内容面向节点运营者. 如果你想存储, 发送和接收 MINA 而不运行节点, 请查看 [ Install a Wallet](../using-mina/install-a-wallet) 页面, 其中包含各类用户友好的 Mina 钱包链接. 

在本节中, 我们将简要概述如何使用 Mina 客户端发送交易, 以及如何与区块链进行交互. 

## Using an offline signed-transaction

如果你想在不自己运行节点的情况下发送交易, 而是委托给运行节点的其他人来进行操作, 请继续阅读此处内容. 如果你希望直接通过正在运行的节点发送交易, 请跳至 [using a connected node](#using-a-connected-node)部分. 

### Using a Ledger device

如果你的私钥存储在 Ledger 设备上, 要离线生成签名交易, 请查看 [Ledger Offline Mode](/using-mina/ledger-hardware-wallet#offline-mode).

### Using a keypair generated with the generate-keypair tool

更好的工具即将推出: https://github.com/MinaProtocol/mina/issues/8928. 目前, 请使用该问题评论中提供的 [workaround](https://github.com/MinaProtocol/mina/issues/8928#issuecomment-857095846). 

### Using a keypair generated with the offline client-sdk

使用 [Mina Signer](./node-operators/mina-signer.md). 有关安装和使用说明, 请查看 Mina Signer 仓库中的 [README](https://github.com/o1-labs/o1js/blob/main/src/mina-signer/README.md). 

### Send the transaction

你可以使用托管服务来广播已签名的交易. 发送已签名的交易**不会**泄露你的私钥. 

使用 Mina Signer 签署的交易可以使用: [https://minascan.io/mainnet/broadcast/payment](https://minascan.io/mainnet/broadcast/payment)

使用 Ledger 硬件钱包签署的交易可以使用: [https://minascan.io/mainnet/broadcast/ledger-payment](https://minascan.io/mainnet/broadcast/ledger-payment)

## Using a connected node

在本节的其余部分, 我们假设你已在系统中安装了 Mina 客户端, 如果你尚未安装 Mina, 请查看 [Getting Started](./node-operators/block-producer-node/getting-started.md). 

## Import your account

一旦我们的节点完成同步, 我们就需要导入公/私钥对, 以便能够签署交易并生成用于接收支付的地址. 出于安全原因, 我们希望将密钥放在攻击者较难访问的目录下. 

运行以下命令来导入你 [previously generated](./node-operators/generating-a-keypair.md) 密钥对文件: 

```
mina accounts import --privkey-path ~/keys/my-wallet
```

系统将提示你输入创建账户时设置的密码. 

> 公钥可以随意与任何人共享, 但要非常小心你的私钥文件. 切勿将此私钥与任何人共享, 因为它相当于你资金的密码. 


该命令的响应如下所示: 

```
😄 Imported account!
Public key: B62qjaA4N9843FKM5FZk1HmeuDiojG42cbCDyZeUDQVjycULte9PFkC
```

此外, 你还可以使用`mina accounts create`命令来生成新的账户, 用于发送和接收交易. 

由于公钥很长且难以记忆, 我们将其保存为环境变量. 使用以下命令, 但需将`<YOUR-PUBLIC-KEY>`替换为上述命令输出的公钥: 

```
export MINA_PUBLIC_KEY=<YOUR-PUBLIC-KEY>
```

现在我们可以在任何地方通过`$MINA_PUBLIC_KEY`来访问它——通过执行`echo $MINA_PUBLIC_KEY`来检查是否已正确保存. 

请注意, 这些环境变量仅会在当前 shell 会话中保存, 因此如果你想将它们保存以供将来使用, 可以将它们添加到`~/.profile`或`~/.bash_profile`文件中. 

如果你在云虚拟机上运行节点, 请确保导出并保存密钥文件. 你可以使用以下命令导出密钥: 

```
mina accounts export --public-key <PUBLIC-KEY> --privkey-path <EXPORT-PATH>
```

然后使用 [scp](https://linux.die.net/man/1/scp) 将其保存到本地机器: 

```
scp <EXPORT-PATH> <LOCAL-DESTINATION>
```

之后, 当启动新的虚拟机时, 你可以上传密钥并导入它: 
```
mina accounts import --privkey-path <PRIVATE-KEY-FILE>
```

如果你忘记了已经创建的密钥对, 可以使用以下命令查看所有密钥对: 

```
mina accounts list
```

## Check account balance

我们可以使用以下命令检查所有账户的余额: 

```
mina accounts list
```

你的账户余额可能显示为`Balance: 0 mina`. 根据网络流量情况, 你的交易可能需要经过几个区块才能完成处理. 


> 你可以运行`mina client status`命令来查看当前区块高度的更新情况. 

## Make a payment

最后到了关键部分--发送我们的第一笔交易! 在发送交易之前, 你需要解锁你的账户: 

```
mina accounts unlock --public-key $MINA_PUBLIC_KEY
```

出于测试目的, 我们将指定你的公钥作为接收方和发送方. 这意味着我们正在给自己发送一笔交易, 你可以通过执行以下命令查看你的公钥: 

```
echo $MINA_PUBLIC_KEY
```

> 如果接收账户尚未接收过任何交易, 将从交易金额中扣除额外的1个 Mina 账户创建费用. 

让我们给自己发送一些 Mina, 看看交易是怎样的情况: 

```
mina client send-payment \
    --amount 1.5 \
    --receiver $MINA_PUBLIC_KEY \
    --fee 0.1 \
    --sender $MINA_PUBLIC_KEY
```

如果你想知道我们在上述命令中输入了什么内容: 

- 对于`amount`, 我们发送的测试值为`1.5`个 Mina, 这个金额足以覆盖账户创建费用. 
- `receiver`是接收交易的账户的公钥, 例如`B62qjaA4N9843FKM5FZk...`. 
- 对于`fee`, 我们使用0.1个 Mina. 
- `sender`是发送交易的账户的公钥, 例如`B62qjaA4N9843FKM5FZk...`. 

如果此命令格式正确, 我们应该会得到如下类似的响应: 

```
Dispatched payment with ID 3XCgvAHLAqz9VVbU7an7f2L5ffJtZoFega7jZpVJrPCYA4j5HEmUAx51BCeMc232eBWVz6q9t62Kp2cNvQZoNCSGqJ1rrJpXFqMN6NQe7x987sAC2Sd6wu9Vbs9xSr8g1AkjJoB65v3suPsaCcvvCjyUvUs8c3eVRucH4doa2onGj41pjxT53y5ZkmGaPmPnpWzdJt4YJBnDRW1GcJeyqj61GKWcvvrV6KcGD25VEeHQBfhGppZc7ewVwi3vcUQR7QFFs15bMwA4oZDEfzSbnr1ECoiZGy61m5LX7afwFaviyUwjphtrzoPbQ2QAZ2w2ypnVUrcJ9oUT4y4dvDJ5vkUDazRdGxjAA6Cz86bJqqgfMHdMFqpkmLxCdLbj2Nq3Ar2VpPVvfn2kdKoxwmAGqWCiVhqYbTvHkyZSc4n3siGTEpTGAK9usPnBnqLi53Z2bPPaJ3PuZTMgmdZYrRv4UPxztRtmyBz2HdQSnH8vbxurLkyxK6yEwS23JSZWToccM83sx2hAAABNynBVuxagL8aNZF99k3LKX6E581uSVSw5DAJ2S198DvZHXD53QvjcDGpvB9jYUpofkk1aPvtW7QZkcofBYruePM7kCHjKvbDXSw2CV5brHVv5ZBV9DuUcuFHfcYAA2TVuDtFeNLBjxDumiBASgaLvcdzGiFvSqqnzmS9MBXxYybQcmmz1WuKZHjgqph99XVEapwTsYfZGi1T8ApahcWc5EX9
Receipt chain hash is now A3gpLyBJGvcpMXny2DsHjvE5GaNFn2bbpLLQqTCHuY3Nd7sqy8vDbM6qHTwHt8tcfqqBkd36LuV4CC6hVH6YsmRqRp4Lzx77WnN9gnRX7ceeXdCQUVB7B2uMo3oCYxfdpU5Q2f2KzJQ46
```

在账户的第一笔交易中, 你可能看不到`Receipt chain hash`, 但在后续交易中, 它将显示收据链哈希列表的头部. 

## Staking and Snarking

一旦你熟悉了创建地址以及发送和接收 Mina 的基础知识, 我们就可以继续探讨 Mina 网络真正独特的部分, 例如  [participating in consensus and helping compress the blockchain](./node-operators/staking-and-snarking.md).

## Advanced

### Sending Many Transactions 

有时你可能希望发送大量交易, 例如, 如果你运营一个质押池, 要向委托给你的人支付奖励. 

以下所有信息均与`3.0.3`版本相关: 

### Rate limiting

目前, 网络上的节点会对来自给定节点的接收消息进行速率限制. 在3.0.3版本中, 你的节点在发送交易时也会遵循此速率限制. 具体而言, 目前的限制是在5分钟的时间窗口内, 每15秒最多发送10笔交易. 如果你尝试以高于此速率发送交易, 你的节点会将它们排队, 随着较旧的交易从用于计算速率限制的时间窗口中过期, 它们会被刷新发送. 你无需自行限制发送这些交易的速率. 

请注意, Mina 守护进程的旧版本不执行此速率限制; 如果你正在运行旧版本, 则应该手动限制交易数量. 由于重新广播交易存在开销, 如果你需要手动进行速率限制, 我们建议每5分钟发送的交易不要超过50笔. 

### My node crashed or disconnected before I could finish sending transactions

Mina 守护进程目前**不会**持久保存交易池. 这意味着如果你的节点在此过程中崩溃, 它将不会知晓你到目前为止已发送的任何交易. 在3.0.3版本中, 你可以重新发送所有交易(方式与之前完全相同), 它们将在网络上重新广播. 

如果你认为自己只是暂时与网络断开连接, 但你的节点仍保持在线(即 gossip 网络可能遗漏了你的一笔或多笔交易), 在3.0.3版本中, 你可以在本地重新发送任何交易, 它们将再次广播到网络中, 即使你的节点认为它们已经被共享过了. 

### Cancelling a transaction and setting a new fee

要取消一笔交易, 你需要在本地交易内存池中拥有所有尚未提交到链上的交易. 这意味着如果你的节点崩溃了(见上文), 你需要重新发送那些之前的交易. 最后, **要取消一笔交易, 你只需发送一笔与要取消的交易具有相同 nonce 但费用更高的交易即可**. 没有最小增量要求, 只需费用稍高一点(并且足够高, 以便区块生产者会选择你的交易). 