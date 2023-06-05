# Warmup

The warmup challenges are intended to get you familiar with the way Capture the Ether works and the tools you need to use.



Warmup挑战旨在让您熟悉 Capture the Ether 的工作方式以及您需要使用的工具。

***

说明：该靶场需要Ropsten测试网络，该网络已经被弃用，无法完成合约的交互，只能写每道题的题解



## Deploy a contract

### 1.题目要求

To complete this challenge, you need to:

1. Install [MetaMask](https://metamask.io/).
2. Switch to the **Ropsten test network**.
3. Get some Ropsten ether. Clicking the “buy” button in MetaMask will take you to a *faucet* that gives out free test ether.

After you’ve done that, press the red button on the left to deploy the challenge contract.↳

You don’t need to do anything with the contract once it’s deployed. Just click the “Check Solution” button to verify that you deployed successfully.



要完成此挑战，您需要：

1. 安装[MetaMask](https://metamask.io/)。
2. 切换到**Ropsten 测试网络**。
3. 获取一些 Ropsten ether。单击 MetaMask 中的“购买”按钮将带你到一个提供免费测试以太币的*水龙头。*

完成后，按左侧的红色按钮部署挑战合约。

部署后，您无需对合同执行任何操作。只需单击“检查解决方案”按钮即可验证您是否已成功部署。



```solidity
pragma solidity ^0.4.21;

contract DeployChallenge {
    // This tells the CaptureTheFlag contract that the challenge is complete.
    function isComplete() public pure returns (bool) {
        return true;
    }
}
```

### 2.题解

第一步是了解怎么操作部署合约，关卡里也写得很清楚了，首先按照metamask，可以直接在chrome的扩展商店搜索安装，然后切换到Ropsten测试链，当然首先是创建钱包设置密码，然后点击Buy按钮去水龙头取一些ether回来

接下来点击页面右边的红色的deploy即可然后在弹出的交易确认里点击submit即可成功将页面所示的合约部署到测试链上，接下来再点击check并确认交易即可





## Call me

### 1.题目要求

To complete this challenge, all you need to do is call a function.

The “Begin Challenge” button will deploy the following contract:



要完成这个挑战，您需要做的就是调用一个函数。

“Begin Challenge”按钮将部署以下合约：

```solidity
pragma solidity ^0.4.21;

contract CallMeChallenge {
    bool public isComplete = false;

    function callme() public {
        isComplete = true;
    }
}
```

Call the function named `callme` and then click the “Check Solution” button.

调用命名的函数`callme`，然后单击“Check Solution”按钮。

### 2.题解

这个挑战的目的是让你调用一下部署的合约里的callme函数，方法其实很多，比较简单的我们可以直接在remix里进行调用，将合约代码复制过去后，先编译一下，然后在Run里面将环境切换为injected web3，然后在下面的deploy处将我们挑战的页面里给出的合约地址填上，点击at address即可
接下来在下方即可直接调用callme函数

![img](https://p1.ssl.qhimg.com/t01de757c96b937614e.jpg)

调用之后点击isComplete就会发现已经变为true，然后即可返回挑战进行check



## Choose a nickname

### 1.题目要求

It’s time to set your Capture the Ether nickname! This nickname is how you’ll show up on the [leaderboard](https://capturetheether.com/leaderboard/).

The `CaptureTheEther` smart contract keeps track of a nickname for every player. To complete this challenge, set your nickname to a non-empty string. The smart contract is running on the Ropsten test network at the address `0x71c46Ed333C35e4E6c62D32dc7C8F00D125b4fee`.



是时候设置您的 Capture the Ether 昵称了！[这个昵称是您在排行榜](https://capturetheether.com/leaderboard/)上的显示方式。

智能合约`CaptureTheEther`会记录每个玩家的昵称。要完成此挑战，请将您的昵称设置为非空字符串。智能合约在地址 的 Ropsten 测试网络上运行`0x71c46Ed333C35e4E6c62D32dc7C8F00D125b4fee`。



Here’s the code for this challenge:

这是这个挑战的代码：

```solidity
pragma solidity ^0.4.21;

// Relevant part of the CaptureTheEther contract.
contract CaptureTheEther {
    mapping (address => bytes32) public nicknameOf;

    function setNickname(bytes32 nickname) public {
        nicknameOf[msg.sender] = nickname;
    }
}

// Challenge contract. You don't need to do anything with this; it just verifies
// that you set a nickname for yourself.
contract NicknameChallenge {
    CaptureTheEther cte = CaptureTheEther(msg.sender);
    address player;

    // Your address gets passed in as a constructor parameter.
    function NicknameChallenge(address _player) public {
        player = _player;
    }

    // Check that the first character is not null.
    function isComplete() public view returns (bool) {
        return cte.nicknameOf(player)[0] != 0;
    }
}
```

### 2.题解

这一关是让我们设置自己的昵称，也就是在排行榜上显示的名字，其实也是调用个函数的事，操作跟上面一样，合约选择CaptureTheEther，地址填上，调用setNickname函数即可，注意参数填上自己昵称的16进制格式，然后用nicknameOf函数就能看到结果了

![img](https://p5.ssl.qhimg.com/t013408b9e56d576dba.jpg)

接下来返回挑战点击begin game按钮就会部署一个合约来检查你是否设置了昵称，check以后就能正式开始我们的闯关之旅了

