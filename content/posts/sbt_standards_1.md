---
title: "SBT 相关标准介绍【一】"
date: 2022-09-05T17:49:20+08:00
draft: false
tags: ['SBT','EIP']
---

现在有很多 SBT 相关的标准和实现(不下十个)，这也就意味着大家对 SBT 理念有很多不同，但最终的标准只有持续的讨论和实践并经过市场的选择才能确定。本文介绍三个比较比较有意思的 SBT 相关 EIP 标准，此系列持续更新。

### [EIP-4973: Account-bound Tokens](https://eips.ethereum.org/EIPS/eip-4973)

Account-bound Tokens (ABT) 中文名可以叫做账号绑定代币，原因好像是作者认为世界各民族对灵魂的理解是不一样的，用账户可能更准确和中立一些。EIP-4973 创建于 2022-04-01 ，早于 Desoc 论文发布。目前 [Otterspace](https://www.otterspace.xyz/) 和 [violet](https://www.violet.co/) 在使用这个标准。处于 Review 阶段 ([EIP 的创建流程](https://eips.ethereum.org/)大致可以视为四个阶段：Draft -> Review -> Last Call -> Final)。代币标准的总结如下：

- 必须不实现 ERC721 的 transfer 相关的接口
- 代币持有人随时都可以将ABT 与自己的地址接触绑定：unequip 函数
- 可主动发送给接收方，也可以由接收方自主 mint : give 和 take 函数
- 支持 ERC721Metadata 所以钱包和交易平台可以复用 NFT 的基础设施，轻易的展示 ABT
- 接口数量很少关键的函数只有刚刚提到的这三个，保持最大的向后兼容性

EIP-4973 作者自己根据标准实现了[一版合约](https://github.com/rugpullindex/ERC4973)

### [EIP-5114: Soulbound Badge](https://eips.ethereum.org/EIPS/eip-5114)

EIP-5114 的一个很不同的点是，这个标准是把 SBT 绑定到 NFT 而不是绑定到地址上。也引起了社区成员的很多讨论，很多人觉得把 SBT 绑 NFT 的需求好像不是很明显。但也有人觉得还是挺有意思（包括我），因为这其实给 NFT 增加了一些新的玩法，比如具有成长性的 NFT。这里也就抛出了一个重要的问题：SBT 到底应该绑到啥上面，绑地址当然最方便和直接，但是也会面临私钥丢失、换地址等问题。一些解决办法是绑定到 DID 或者使用社交恢复等方法，这里不展开，后面另开文章讨论。回到 EIP-5114 ，此标准创建于 2022-05-30 目前依然是 Draft 阶段，总价如下：

- SBT 铸造时会绑定到某个 NFT 上，只能铸造一次并且不可转移
- SBT 也不可销毁，SBT 的内容也不可变
- 对于 tokenUri 的数据格式不限制

### [EIP-5192: Minimal Soulbound NFTs](https://eips.ethereum.org/EIPS/eip-5192)

从名字上就可以看出这个 EIP 很简洁，它旨在最大限度兼容现有的基础设施，即作为 EIP-721 的一个扩展。标准总结如下:

- EIP-5192 的函数只有一个：locked，当 NFT 状态为 locked 时则代表这是一个灵魂绑定代币，即所有转移 NFT 的函数调用都会报错
- 钱包或者第三方应用可以通过 EIP-165 来查询一个 NFT 状态是否为 locked，即是否是一个 SBT

目前 EIP-5192 已经进入 Last Call 阶段，9 月 12 日如果没有发现什么问题的话，就会进入 Final 阶段，就是到时候差不多可以在正式项目中使用了，EIP-5192 其实是让 SBT 的最快落地的一种方式了吧，因为简单不需要对原有标准有太多更改，也不会有太多安全问题，并且能最大限度的使用现有的基础设施。

---

上述三个 SBT 标准，都是进入 EIP 创建流程的，下次会介绍一些其他类型的，比如结合 zk 的 SBT。如有错误欢迎指正。