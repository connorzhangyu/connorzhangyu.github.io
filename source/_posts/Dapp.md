---
title: Dapp
tags:
  - Mac
  - Solidity
  - Remix
  - 区块链
  - 智能合约
  - Dapp
  - web3
categories:
  - 区块链
abbrlink: 2658
cover: https://image.runtimes.cc/202505281040635.webp
date: 2025-05-29 10:23:17
updated: 2025-05-29 10:23:17
---

# HelloWorld合约

[Remix编辑器](https://remix.ethereum.org)

![image-20250524144934373](https://image.runtimes.cc/202505281038720.png)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract helloworld{
    function helloWorld() public pure returns (string memory) {
        return "Hello, World!"; 
    }
}
```

![image-20250524145024856](https://image.runtimes.cc/202505281038400.png)

# 发行代币

在闲鱼,淘宝上买些测试币,也可以通过水龙头获取(只是有点要求和门槛,不如直接买),这里我买了两笔
![](https://image.runtimes.cc/202505281036642.webp)

新建合约文件

![image-20250529101100930](https://image.runtimes.cc/202505291022584.png)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyError20Token is ERC20{

    constructor(string memory _name,string memory _symbol) ERC20(_name,_symbol){
        _mint(msg.sender,10000 * 10 ** 18);
    }
}
```

![image-20250529101412816](https://image.runtimes.cc/202505291022541.png)

![image-20250529101510973](https://image.runtimes.cc/202505291022024.png)

![image-20250529101815851](https://image.runtimes.cc/202505291022071.png)

![image-20250529102052827](https://image.runtimes.cc/202505291022160.png)
