---
id: reliability-test-2x
title: 可靠性测试
---

## 概述

可靠性指的是软件系统在规定的时间内以及规定的环境条件下，完成规定功能的能力。Phoenix是一个消息驱动的有状态微服务框架，其可靠性我们重点关心以下几个能力：

1. 处理可靠性：使用案例发起一笔同步调用，如果调用成功，必定能查询到调用处理的结果。
2. 重启可靠性：Phoenix为了保证内存状态，会在重启后做EventSourcing，需要测试重启后，EventSourcing是否正常。
3. 投递可靠性：Phoenix可以保障所有到达kafka的消息可靠的消费，并且可以保证后续每一个actor的最少一次投递，并且Phoenix内置消息幂等可以保障消息可靠处理。

下面分别对上述维度的可靠性进行测试。


## 处理可靠性测试

### 测试场景

Phoenix框架处理可靠性基于bank-account服务进行。我们通过模拟一笔资金划拨指令，观察系统处理结果后的前端页面，证明Phoenix可以正确处理请求。

### 校验方法

观察bank-account-server服务的前端页面，最终的处理结果无误则证明Phoenix具有处理可靠性。

### 测试步骤

 1. 使用bank-account服务，在前端发起一笔资金划拨。给账户[account_1]划拨100元等待请求返回。
    ![show](../../assets/phoenix2.x/phoenix-test/reliability/001.png)

 2. 请求返回结果为划拨成功后，刷新前端页面数据，查看所有账户的资金数据。
    ![show](../../assets/phoenix2.x/phoenix-test/reliability/002.png)

 3. 由查询结果可知，账户[account_1]的最终金额为1100元（每个账户默认有1000元的初始资金）。Phoenix正确处理了前端请求，具有处理可靠性。

## 重启可靠性测试

### 测试场景

Phoenix框架重启可靠性基于bank-account示例应用进行。通过模拟应用处理完毕批量请求后，进行服务重启。证明重启完毕后，应用数据能恢复到重启前的状态。

### 校验方法

观察bank-account-server服务处理批量请求并重启这一操作前后的前端页面，一致则证明Phoenix具有重启可靠性。

### 测试步骤

 1. 使用bank-account服务，在前端发起批量资金划拨。全部处理完毕后，刷新页面数据，查看所有账户的资金数据。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/003.png)

 2. 待请求全部处理完毕后，重启服务节点。

 3. 重启完毕后，刷新页面数据，查看所有账户的资金数据。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/005.png)

 4. 对比重启前后的账户表格，发现完全一致。Phoenix具有重启可靠性。

## 投递可靠性测试

### 测试场景

Phoenix目前会对所有投递到Kafka中的消息做可靠性消费，并且保证可靠的投递给下游并产出结果，Phoenix可以检测事务超时未结束时发起重试来达到可靠性投递。
在bank-account应用中对特定账户`monitor_retry`的划拨模拟了网络堵塞。具体测试场景如下：

1. 压测前关闭Phoenix服务端，待压测结束后再启动服务端，观察压测的消息都可以正确无误的处理。
2. 然后通过下单页面使用固定账户`monitor_retry`发起划拨请求，期望触发系统重试投递。


### 校验方法

1. bank-account-server服务启动后，Kafka的消息被正确处理，通过账户页面查询到处理结果正确。
2. 发起`monitor_retry`账户划拨请求后，前端会报rpc超时，查看Grafana和账户详情，证明中间发生了重试最终达到消息正确处理。

### 测试步骤

1. 压测前停掉bank-account-server应用，使用压测页面下单100笔
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/006.png)

2. 请求结束后，启动bank-account-server，观察Grafana发现所有消息都正确处理，账户详情结果正确。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/007.png)

3. 使用固定账户发起划拨请求`monitor_retry`。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/008.png)

4. 观察Grafana处理情况，一段时间后会可以看到有`RETRY_MESSAGE`指标产生，证明系统产生了重试。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/009.png)

5. 查看账户详情页面，步骤4因为超时发起重试的消息也被正确处理了。
 ![show](../../assets/phoenix2.x/phoenix-test/reliability/010.png)


## 结论
通过上面三个维度的可靠性测试，证明Phoenix作为基于内存的消息驱动框架，可以在框架层面保证状态正确修改，消息的可靠性投递，状态的正确恢复。

