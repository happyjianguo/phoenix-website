---
id: reliability-test-2x
title: 可靠性测试
---

### 可靠性测试定义

> 是软件系统在规定的时间内以及规定的环境条件下,完成规定功能的能力。

### 为什么需要可靠性测试

Phoenix作为分布式微服务架构，必须要保证每一步的可靠性，比如说处理能力的可靠性，消息流转的可靠性（即消息不丢，传则必达）。

由于Phoenix是基于EventSource思想打造的架构，因此我们也必须保证EventSourcing的可靠性。

> EventSourcing(时间溯源)将对应用程序状态的所有更改存储为一系列事件。 我们不仅可以查询这些事件，还可以使用事件日志来重建过去的状态，并作为自动调整状态以应对追溯性变化的基础。

### 测试方案

#### 测试场景

- 处理可靠性
  1. 使用bank-account服务，在前端发起一笔资金划拨。给账户[account_1]划拨100元等待请求返回。
  2. 请求返回结果为划拨成功后，刷新前端页面数据，查看所有账户的资金数据。
- 重启可靠性
  1. 使用bank-account服务，在前端发起批量资金划拨。全部处理完毕后，刷新页面数据，查看所有账户的资金数据。
  2. 待请求全部处理完毕后，重启服务节点。
  3. 重启完毕后，刷新页面数据，查看所有账户的资金数据。
- 投递可靠性
  1. 在测试环境部署多活bank-account服务，实例数量为3个。在服务启动正常后以固定流量发送批量划拨请求并持续一定时间。
  2. 在前端的持续过程流量中，使用工具阻断3个实例之间的网络，并恢复。这个操作持续多次。
  3. 停止流量，待所有请求处理完毕。观察前端页面的所有划拨数量和步骤1中的划拨总次数是否一致。

#### 校验方法

* 在[处理可靠性]测试中，请求处理完毕后，在前端页面中查询账户[account_1]的总账户金额应为1100元。（每个账户默认有1000元初始资金）
* 在[重启可靠性]测试中，步骤3和步骤1中所有账户的资金数据应保持一致。
* 在[投递可靠性]测试中，步骤3中页面上观察到的划拨数量和步骤1发起的批量划拨请求中的划拨数量一致。

### 测试步骤

### 测试结果