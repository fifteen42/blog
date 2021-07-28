---
title: "NNS 治理系统概念总结"
date: 2021-05-18T15:33:50+08:00
tags: ['区块链','Internet Computer','NNS']
draft: false
---

[Understanding the Internet Computer’s Network Nervous System, Neurons, and ICP Utility Tokens](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8) 是官方对 互联网计算机（Internet Computer）的网络神经系统（Network Nervous System, 简称 NNS）的讲解，内容包括 NNS，神经元，ICP 代币经济。本文对其中的关键概念进行了梳理和总结。

> 注意，原文主要描述了 NNS 治理系统的经济层面的设计细节，需要对互联网计算机、canister等概念有一定理解。

---

### 概况

互联网计算机（Internet Computer）是 Dfinity 基金会推出的自适应区块链，其智能合约（canister）可以承载传统 web 应用，因此也可以理解为一种去中心化的云服务平台（类比AWS、阿里云）。

互联网计算机由网络神经系统（NNS）治理，NNS 相当于互联网计算机的大脑。

NNS 本身也是互联网计算机的子网之一，所以升级和调整不会打断互联网计算机的运行。

NNS 可以做到的事情有：升级节点以更新协议或修复漏洞、调整经济参数、添加新的子网以扩容、拆分子网、冻结恶意应用等等。

用户可以质押 ICP 代币生成神经元，从而参与到 NNS 中进行投票治理并获取投票奖励。

用户通过神经元向 NNS 发起提议，其他神经元针对提议投票（接受或者拒绝）。

每个提议都会有一个类型，比如 “#NodeAdmin” “#NetworkEconomics”，代表着不同类型的事务。

如果提议被接受，它将会立即自动化执行。

如果提议被拒绝，发起提议的神经元将会被收取一定费用，以避免过多的 “垃圾提议”。

### 神经元

用户可以锁定 ICP 代币生成神经元，参与投票治理并获取投票奖励。

神经元创建成功后，开始计算年龄。

随着投票活动的增加，神经元的成熟度将会随之增加。

当神经元的成熟度达到某一阈值，可以生成一个新的神经元作为奖励，新神经元中的代币数量为原神经元的代币数量乘以原神经元的成熟度。

用户需要溶解神经元才能取回对应代币，溶解所需的时间称为溶解延迟。

只有溶解延迟超过 6 个月的神经元才能参与投票治理以及发起提议。

可以向神经元转账以增加质押量，但年龄以及成熟度会按比例缩减。

溶解延迟由用户在创建神经元时设置，溶解延迟最多 8 年。

溶解过程中，用户可以随时停止溶解回到锁定状态，但神经元的年龄会重新从 0 开始计算。

处于溶解状态多久，溶解延迟会减少对应的时间。

溶解延迟可以自行增加。

溶解延迟只能通过溶解操作来减少，不能手动减少。

神经元可手动投票，也可以跟随一组（或一个）神经元自动投票。

神经元的投票权以及投票奖励和这几个因素相关：代币质押数量、溶解延迟、年龄、所参与的投票活动占所有投票活动的比例。

### ICP 代币

用户可将 ICP 代币质押为神经元，参与投票治理并获得奖励。

开发者可将 ICP 可转化为 cycle 为其智能合约（canister）提供计算资源。
  
  - ICP 转化为 cycle 的过程是单向的
  - ICP 转化为 cycle 的转化汇率是变化的，以保证 cycle 的实际成本保持恒定

NNS 会为节点提供者（数据中心）提供 ICP 作为奖励。


### 账本（Ledger canister）

互联网计算机有不同级别的子网，NNS 子网的等级比较高，会承载一些重要的 canister。

ICP 账本也是一个智能合约（canister），其运行在 NNS 子网中。

账本像一张表记录着所有账户的余额，每一行叫做一个账户，每一行有两个值：

-  **账户 ID**：由控制账户的 principal 衍生而来，控制账户可以是公钥对也可以是 canister
-  **余额（数值单位是 ICP 的 亿分之一 ）**：账户余额

当控制账户是一个公钥对时，它能对一个账户做以下两个操作

-  **发送 ICP**：向另外一个账户转账，如果代币余额为 0，此账户就会从 Ledger canister 中删除。
-  **通知**：如果向 NNS canister 发送交易的话，可以要求 Ledger canister 通知对应的 NNS canister 以便其能够根据通知行动。这个功能的两个例子是：创建神经元；更新一个神经元的质押代币数量。

### 奖励计算

Dfinity 基金会认为 90% 的 ICP 代币应该被锁定在神经元中，所以奖励也是根据这个值来设计的，当锁定在神经元的 ICP 代币少于 90% 时，希望会提供更高的奖励来鼓励参与者质押 ICP 代币。

给神经元的奖励初始值每年总供应量的 10% 都会作为奖励，这个比例会随着网络的稳定而逐步下降，最终会在第八年达到 5%，之后保持 5% 不变。

神经元的成熟度最开始为 0% ，随着投票活动而逐渐增加成熟度。

当神经元成熟度达到某个阈值之后，它就可以生成一个新的神经元，新神经元中质押的 ICP 即为奖励，其数值等于父神经元质押的 ICP 数量乘以成熟度。

比如，一个质押了 100 个 ICP 代币且成熟度为 10% 的神经元，可以生成一个锁定 10 个 ICP 代币的且年龄与成熟度都为 0。

新神经元的溶解延迟为一天，年龄为 0 天，即一天后即可溶解取出 ICP 代币。

指定时刻神经元投票权的计算方法：神经元质押量 * 溶解延迟奖励数 * 年龄奖励数
  - 溶解延迟奖励系数介于 1 到 2 之间，根据溶解延迟线性变化（溶解延迟上限为 8 年）
  - 年龄奖励系数介于 1 到 1.25 之间，根据年龄线性变化（上线 4 年）。神经元进入锁定状态之后，开始计算年龄。
  - 举个例子：一个质押 100 个 ICP 的神经元，如果其溶解延迟为 8 年，且年龄为 4 年，则如果他的投票权等于 100 * 2 * 1.25 ，达到最大值 250。

分发奖励的方式为，统计当前已经结束投票但是还未计算投票奖励的提议，计算所有神经元在这些提议上的投票权，然后根据每个神经元在这些提议上所投出的投票权分配奖励。所以神经元由动力尽可能多的参与投票活动。

---

> 以下为提议主题，提议类型的具体描述，以及神经元属性以及操作的详细介绍，不看也不影响整体理解。

### 提议

每个提议都有以下几个参数：

- **ID**：提议的唯一标识，由 NNS 分配给每个提议
- **Summary**：概要，不超过 280 字节
- **URL**：存放提议内容的网址
- **Proposer**：发起提议的神经元的 ID
- **Proposal Type**：提议的类型
- **Parameters**：相关参数

提议对应的主题，由提议的类型推导出来，决定了提议的处理方式。初始的主题有：

- **#NeuronManagement**：只对特定神经元有效，通过这类协议，神经元可以被其领投者管理
- **#ExchangeRate**：提供 ICP 市场价格，通过 IMF SDR 来衡量，NNS 根据这个来调整 ICP 与 cycles 的转化率。由于此主题十分频繁，所以投票期很短，并且不记录于神经元的投票历史中。
- **#NetworkEconomics**：更改系统经济参数，比如调整给数据中心的奖励
- **#Governance**：治理相关，比如调整某个具体的参数
- **#NodeAdmin**：节点管理，比如升级节点程序
- **#ParticipantManagement**：管理网络参与者，比如新增数据中心 ID
- **#SubnetManagement**：子网管理，比如新增子网
- **#NetworkCanisterManagement**：管理系统级子网，比如升级 NNS 子网
- **#KYC**：更新 KYC 信息，比如创世纪分发代币时需要 KYC 信息
- **#NodeProviderRewards**：对节点提供者的奖励

提议类型有：

- **ManageNeuron (#NeuronManagement, Restricted Voting)**：只针对指定的目标神经元起效，只有目标神经元的领投者才能对这些提议投票。这为多人共同管理重要神经元提供了一种安全而方便的方法。
- **ManageNetworkEconomics (#NetworkEconomics)**：可以更新一些经济参数
  - **Reject cost**: 提议失败后要收取的费用
  - **Minimum Neuron Stake**：最小神经元质押量
  - **Neuron Management fee**：神经元管理费用
  - **Minimum ICP/SDR rate**: ICP/SDR 的下限
  - **Dissolve delay of spawned neurons**：新生神经元的溶解延迟
  - **Maximum node provider rewards**：节点提供者的奖励
  - **Transaction fee**：账本交易的手续费
  - **Maximum number of proposals to keep per topic**：每个主题最大容纳的提议数量，超过此数，最开始的一些提议的缓存将被清除
- **Motion (#Governance)**：一段文本，可以拒绝或接受。不涉及具体的代码执行，而是作为指导 IC 生态的战略
- **ApproveGenesisKYC (#KYC)**：在创世纪被创建的神经元的 GenesisKYC 为 false，这样的神经元不能产生新的神经元，溶解延迟到期后也不能取出代币。本提议为一些 ID 的 GenesisKYC 设置为 true。（创世纪的神经元都需要进行 KYC，创世纪之后创建的神经元 GenesisKYC = true）
- **AddOrRemoveNodeProvider (#Participant Management)**: 为节点提供方添加ID
- **RewardNodeProvider (#NodeProviderRewards)**：想 Gen-1 节点提供奖励
- **SetDefaultFollowees (#Governance)**：设置投票领投者

下面是一些可以调用其他 NNS canister 的提议类型：

- **CreateSubnet (#SubnetManagement)**：从数据中心和运营商提取节点，然后组成新子网
- **AddNodeToSubnet (#SubnetManagement)**：向子网添加新的节点
- **InstallNetworkCanister (#NetworkCanisterManagement)**：向 NNS 子网添加新的 canister。由 root canister 来管理
- **pgradeNetworkCanister (#NetworkCanisterManagement)**：更新 NNS 子网的 canister，由 root canister 控制
- **BlessReplicaVersion (#NodeAdmin)**：对副本版本的提议
- **RecoverSubnet (#SubnetManagement)**：恢复停滞的子网
- **UpdateSubnetConfig (#SubnetManagement)**：更新子网配置
- **AssignNOID (#ParticipantManagement)**：向节点运维者提供一个身份，以关联对应所有权
- **RootUpgrade (#NetworkCanisterManagement)**：更新 root canister
- **SetICPSDR (#ExchangeRate)**：指导 NNS 根据 IMF SDR 确定 ICP 的市场价格
- **UpgradeSubnetToReplicaVersion (#SubnetManagement)**：升级指定子网的副本版本
- **ClearProvisionalWhitelist (#NetworkEconomics)**：清除临时白名单，白名单中的 ID 可以使用 cycles 创建 canister，只能在测试或者引导中使用
- **RemoveNodeFromSubnet (#SubnetManagement)**：从子网中移除节点
- **SetAuthorizedSubnetworks (#Governance)**：通知负责铸造的 canister ，某一 ID 被授权使用某个子网
- **SetFirewallConfig (#SubnetManagement)**：更改防火墙配置，子网区块链能与之交互的节点范围
- **UpdateNodeOperatorConfig (#NodeAdmin)**：更改节点运维者的补贴
- **StopOrStartNNSCanister (#NetworkCanisterManagement)**：停止或者启动一个 NNS 子网


### 神经元

神经元有以下属性

- **Identity (uint64)**：神经元 ID，跟随神经元就是用的这个值，是一个 64 位随机值
- **Account (bytes, private)**：锁定 ICP 的 Ledger 账户
- **Controller (principal ID, private)**：神经元的控制者，一个控制者可以有多个神经元
- **Hot Keys (list of principal ID, private)**：可执行特殊操作（比如投票）的密钥，而不会暴露地址
- **CreatedAt (timestamp)**：神经元创建时间
- **AgingSince (timestamp)**：神经元的年龄，要么是创建时间，要么是最后一次停止溶解的时间
- **DissolveState**：溶解状态，任何时候只能在 WhenDissolved (timestamp) 或者 DissolveDelay (duration)中指定一个  
  - **WhenDissolved** 开始溶解时，WhenDissolved 会计算溶解完毕的 Unix 时间戳，当用户停止溶解时，DissolveDelay 等于 WhenDissolved 减去执行停止操作时的时间戳。
  - **DissolveDelay** 溶解停止之后，这个值记录着溶解延迟的时间，最多 8 年
- **Maturity**：成熟度（百分比），决定了生成新神经元的能力以及新神经元中的 ICP 数量。新神经元创建时，成熟度为 0%，当神经元投票，NNS 会给他们逐渐增加成熟度
- **Follow Relationships**：私有的，对他人不可见，针对每一个主题，神经元可跟随其他神经元自动投票。
- **Recent Votes (public)**：最近投票活动，公开可见
- **NotForProfit (boolean)**：神经元是否以营利为目的，如果为 true, 则可以投票决定溶解

可以计算出来的值

- **Age (seconds)**：年龄：自创建神经元以来上一次溶解以来经过的时间
- **State (LOCKED or DISSOLVING or DISSOLVED)**：状态，包括 锁定，溶解中，已溶解
  - 锁定：在此状态下年龄随着时间逐渐增加，若溶解延迟不小于 6 个月则可以参与投票
  - 溶解中：在此状态下溶解延迟随时间逐渐减少，并且年龄始终为 0 
  - 已溶解：在此状态下，神经元所质押的 ICP 代币可以被取出，溶解延迟记为 0，所以不能投票
- **ControlByProposals (boolean)**：如果一个神经元在 ManageNeuron 主题上设置了领投神经元，那么领投神经元们可以通过 ManageNeuron 类型的提议来管理此神经元。这为神经元的管理提供了一种安全而便捷的管理方式

神经元的控制者可以执行以下命令

- **Start Dissolving**：开始溶解，溶解延迟开始减少
- **Stop Dissolving**：停止溶解，溶解延迟停止减少
- **Disburse**：从神经元中取出资金，只有当溶解延迟为0时，才能执行
- **Increase Dissolve Delay**：增加溶解延迟
- **Spawn**：当神经元达到一定成熟程度后，就可以繁殖新的神经元，这个神经元可以被其父神经元的 principal 控制，也可以指定新的神经元。繁殖新的神经元后，父神经元的成熟度降为 0。
- **Add Hot Key**：添加热密码（webauthn），这比冷密码（助记词，私钥等）更加安全，提供管理神经的一种更好的方式
- **Remove Hot Key**：移除热密码

使用神经元配置好的 principal 或者热密码可以执行以下操作

- **Vote**：通过神经元投票接受或者拒绝某个提议
- **Follow**：针对某一主题为神经元添加跟随逻辑：根据所跟随的人里面的投票结果自动投票。如果是刚好对半的话，那么投拒绝。

### 每年ICP 代币供应量中给神经元的奖励占比计算公式

R(t) = 0.05 + 0.05[ (G + 8y – t) / 8y ]²

R(t) 为 t 时刻的奖励，t 为创世纪后 8 年内的任意时刻，0.05 为最终的奖励比例，0.05为最初的奖励比例 0.1 减去最终的奖励比例 0.05。

具体函数为：

```Rust
fn nominal_voting_rewards_available(icp_supply, days_since_genesis)
{
  // 8年共计 2922 天
  let day = min(days_since_genesis, 2922);
  let as_perc_of_annual = 0.05 + 0.05 [ (2922 — day) / 2922 ]^2
  icp_supply*as_perc_of_annual / 365.25
}
```