地址：
https://github.com/flyq/LeBlock

功能模块：
1、Excharge.sol
   功能：在以太坊主网上用ETH兑换TAT。
   部署位置：以太坊主网
   状态：完成

2、Gateway.sol
   功能：用户将以太坊主网上的TAT充值到侧链或者用户将侧链上的TAT提取到以太坊主链。
   部署位置：以太坊主网
   状态：完成

3、Register.sol
   功能：用户将自己的拼搭物上以太坊主网，注册版权。
   部署位置：以太坊主网
   状态：完成
   备注：直接将拼搭物的数据结构上IPFS，然后将IPFS的地址(拼搭物数据结构hash)上链，这部分已经完成。
   听周老师建议，所以需要一个八叉树和Merkle树的结合的结构。这样既能压缩数据，又方便版权确认。
   这部分还在思考，不过这部分应该是上IPFS之前就要完成的，上链的还是根hash

4、exchangeBetween20token.sol
   功能：实现玩家之间基础乐块(基础乐块是ERC20token)之间的交易。
   部署位置：以太坊主网
   状态：未完成

5、happyBlock.sol
   功能：维护全网玩家动态算力，实现矿机三种状态，根据算力随机挖矿(算力多少决定玩家得到基础乐块的概率)。
   部署位置：侧链
   状态：废弃
   备注：这是基础乐块按照ERC721token实现的(要按照ERC20实现)，而且动态算力和挖矿算法实现得有点复杂。
   不过里面的函数可以借用

6、Leblock_main.sol
   功能：happyBlock改进
   部署位置：侧链
   状态：未完成
   备注：还有一些功能没实现。矿机相关的功能在这里实现

7、Leblock.sol
   功能：基础乐块的实现，一种ERC20token
   部署位置：以太坊主网
   状态：完成

通用模块：
1、AddressUtils.sol
   功能：对地址进行检测
   状态：完成

2、ERC721Token.sol
   标准的ERC721实现，用户的拼搭物是一个721Token。
   状态：完成

3、Owned.sol
   功能：设置管理员等
   状态：完成

4、Pausable.sol
   功能：设置风险管理等。(能够在某些紧急情况下暂停合约部分函数)
   状态：完成

5、QueueStru.sol
   功能：实现队列数据结构
   状态：完成

6、SafeMath.sol
   功能：安全运算
   状态：完成

7、SafeMathInt.sol
   功能：安全运算 
   状态：完成

8、StringUtils.sol
   功能：字符串比较功能
   状态：完成

9、BytesUtils.sol
   功能：byte转换成base58，如果IPFS的地址用bytes存放，需要这个功能。目前的做法是IPFS地址直接用String类型，弊端是gas高一些。
   状态：完成


现在侧链还在选型，一些实现受侧链限制。