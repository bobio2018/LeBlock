# 已有的合约
1. Gateway_notary.sol(MC/TC):通过公证人的方法实现跨链。  
2. WareHouse.sol(TC):用Oracle的方案实现仓库。Oracle没有实现，但是能够测试。  
3. WareHouse_Admins.sol(TC):没使用Oracle的方案实现仓库。  
4. CopyrightCenter.sol(TC):侧链上的版权中心。  
5. CopyrightCenter_MC.sol(MC):主链上的版权中心。  
6. BP.sol(TC/MC):侧链上的BP、CC(已shelf了的BP)表示者，主链上的CC表示者。  
7. Leblock.sol(TC/MC):侧链以及主链上的AB表示者。  



# 测试流程
## 跨链转账
### TC => MC
侧链：  
1. 侧链上部署`Leblock.sol`, 假设其名称为`ab1`, 地址为`ab1`, 主链上部署`Leblock.sol`, 名称为`AB1`, 地址为`AB1`. 部署`Leblock.sol`时注意输入符号, 名字, 总供应量:
`constructor(string _symbol, string _name, uint256 _totalSupply) public`, 
其中总供应量会全部给部署合约的人的地址  
2. 在侧链以及主链上部署`Gateway_notary.sol`, 侧链上的地址为`g1`, 主链上的为`G1`  
3. 玩家A在`ab1`上有`100 ab1`, 玩家A调用`ab1`上合约的`approve`, 输入`g1`, `100*10^18`.  
4. 玩家A调用`g1`上合约的`deposit20`, 输入`100*10^18`, `ab1`  
主链：  
5. 官方先从`AB1`转足够的`AB1`到`G1`地址下, 然后官方从链下得知玩家A已经在侧链上充值了, 官方调用`G1`的`withdraw20`, 输入`100*10^18`, `AB1`, `玩家A在主网上地址`，`txhash`，等额的token将会转给玩家A在主网上的地址。`  

### MC => TC
和上面类似  


## WareHouse的compose和deCompose
### 基于Oracle版本
侧链：
1. 在侧链上部署两个合约`Leblock.sol`, 其名称为`ab1`, `ab2`(注意其他量的初始化). 玩家地址A下有`ab1`, `ab2`各`10 * 10^18`. 
2. 在侧链上部署`WareHouse.sol`, 地址为`wh`.  
3. 在侧链上部署`BP.sol`, 地址为`bp`(注意初始化量, 名字, 符合).  
4. 玩家A调用`ab1`, `ab2`的`approve`函数, 参数为`wh`, `10 * 10^18`(其实estmate没有要求这么多，第一个`1 * 10^18`, 第二个`2 * 10^18`).  
5. 玩家上传数据到IPFS, 并得到一个`BPhash`(链下).  
6. 官方(`owner`)调用`wh`上的`addABaddress`两次, 以此把`ab1`, `ab2`地址传进去. 最好调用`getABaddress`check一下.  
7. 官方(`owner`)调用`wh`上的`changeBPaddress`, 把`bp`传进去.   
8. 官方调用`BP`的`addAdmins`, 将wh的地址传进去. 
9. 玩家A调用`wh`的`compose`, 传入`BPhash`, 正常执行. 至此`compose`流程走完. 玩家A将减少`1 * 10^18`的`ab1`以及`2 * 10^18`的`ab2`, 将得到一个BP.  

10. 玩家A调用`wh`的`deCompose`, 传入`8步骤`里面的`BPhash`, 即能销毁BP，得到AB  

### 基于Admin版本（没有Oracle）
侧链:  
1. 在侧链上部署两个合约`Leblock.sol`, 其名称为`ab1`, `ab2`(注意其他量的初始化). 玩家地址A下有`ab1`, `ab2`各`10 * 10^18`.  
2. 在侧链上部署`WareHouse_Admins.sol`, 地址为`wh`.  
3. 在侧链上部署`BP.sol`, 地址为`bp`(注意初始化量, 名字, 符合).  
4. 玩家A调用`ab1`, `ab2`的`approve`函数, 参数为`wh`, `10 * 10^18`(其实estmate没有要求这么多，第一个`1 * 10^18`, 第二个`2 * 10^18`).  
5. 玩家上传数据到IPFS, 并得到一个`BPhash`(链下).  
6. 官方(`owner`)调用`wh`上的`addABaddress`两次, 以此把`ab1`, `ab2`地址传进去. 最好调用`getABaddress`check一下.  
7. 官方(`owner`)调用`wh`上的`changeBPaddress`, 把`bp`传进去.  
8. 官方调用`BP`的`addAdmins`, 将`wh`的地址传进去. 
9. 玩家A调用发起请求给PS,将`BPHash`传给PS(链下).
10. 官方(`owner`)去调用`wh`的`compose`, 传入`BPhash`, `玩家A地址`, `对应BP消耗的` 正常执行. 至此`compose`流程走完. 玩家A将减少`1 * 10^18`的`ab1`以及`2 * 10^18`的`ab2`, 将得到一个BP.  

11. 玩家A调用`wh`的`deCompose`, 传入`8步骤`里面的`BPhash`, 即能销毁BP，得到AB  


## CopyrightCenter的shelf和unshelf
### 侧链
`(1-9)`和基于`Admin`版本的`compose`的`(1-9)`相同
1. 在侧链上部署两个合约`Leblock.sol`, 其名称为`ab1`, `ab2`(注意其他量的初始化). 玩家地址A下有`ab1`, `ab2`各`10 * 10^18`.  
2. 在侧链上部署`WareHouse_Admins.sol`, 地址为`wh`.  
3. 在侧链上部署`BP.sol`, 地址为`bp`(注意初始化量, 名字, 符合).  
4. 玩家A调用`ab1`, `ab2`的`approve`函数, 参数为`wh`, `10 * 10^18`(其实estmate没有要求这么多，第一个`1 * 10^18`, 第二个`2 * 10^18`).  
5. 玩家上传数据到IPFS, 并得到一个`BPhash`(链下).  
6. 官方(`owner`)调用`wh`上的`addABaddress`两次, 以此把`ab1`, `ab2`地址传进去. 最好调用`getABaddress`check一下.  
7. 官方(`owner`)调用`wh`上的`changeBPaddress`, 把`bp`传进去.  官方调用`BP`的`addAdmins`, 将wh的地址传进去. 
8. 玩家A调用发起请求给PS,将`BPHash`传给PS(链下).
9. 官方(`owner`)去调用`wh`的`compose`, 传入`BPhash`, `玩家A地址`, `对应BP消耗的` 正常执行. 至此`compose`流程走完. 玩家A将减少`1 * 10^18`的`ab1`以及`2 * 10^18`的`ab2`, 将得到一个BP.

10. 在侧链上部署`CopyrightCenter.sol`, 地址为`cc`.
11. 在侧链上部署`BP.sol`, 并初始化("CR","cr"), 地址为`cr`, 作为侧链上的版权作品中心.
12. 官方(`owner`)调用`cc`的`setBPaddress`, `setCRaddress`, `setWHaddress`, 分别传入`bp`, `cr`, `wh`.
13. 官方(`owner`)调用`cr`的`addAdmin`, 传入`cc`, 把`cc`设置为`cr`的admin.
14. 官方(`owner`)调用`wh`的`addAdmin`, 传入`cc`, 把`cc`设置为`wh`的admin.
15. 官方(`owner`)调用`cc`的`shelf`, 传入上述步骤的`BPhash`, `玩家A`的地址, 就会锁住`BPhash`对应的`bp`, 生成一个`cr`
16. 官方(`owner`)调用`cc`的`unshelf`, 传入`BPhash`, 就会解锁对应的`bp`, 删除一个`cr`

### 主链
1. 在侧链上部署`BP.sol`, 并初始化("CR","cr"), 地址为`cr`, 作为主链上的版权作品中心.
2. 在侧链上部署`CopyrightCenter_MC.sol`, 地址为`cc_mc`.
3. 官方(`owner`)调用`cc_mc`的`setCRaddress`传入`cr`.
4. 官方(`owner`)调用`cr`的`addAdmin`, 传入`cc_mc`, 把`cc_mc`设置为`cr`的admin.
5. 官方(`owner`)调用`cc_mc`的`shelf`, 传入上述步骤的`BPhash`, `玩家A`的地址, 就会锁住`BPhash`对应的`bp`, 生成一个`cr`
6. 官方(`owner`)调用`cc`的`unshelf`, 传入`BPhash`, 就会解锁对应的`bp`, 删除一个`cr`




ropsten测试环境：  
`ab1`:      
0x200A363b71b84F6120C1D112774E11A51B79a49b  
https://ropsten.etherscan.io/address/0x200A363b71b84F6120C1D112774E11A51B79a49b#code  
  
`ab2`:  
0xE090EEd19A0a1CC26B31624cf0BfCF529c19bd2f  
https://ropsten.etherscan.io/address/0xE090EEd19A0a1CC26B31624cf0BfCF529c19bd2f#code  
  
`WareHouse_Admins`:   
0x5dCE0e6C6Bb37cB0448c956BAA4c31Fa1749Bba1  
https://ropsten.etherscan.io/address/0x5dce0e6c6bb37cb0448c956baa4c31fa1749bba1#code  
  
`BP`:  
0x53195e895326b90983d40425301BeF4d749DF5B6  
https://ropsten.etherscan.io/address/0x53195e895326b90983d40425301BeF4d749DF5B6#code  
  
`CopyrightCenter`:  
0x40c80d17d16f50cc672d55bde8c6661ef6ec0833  
https://ropsten.etherscan.io/address/0x40c80d17d16f50cc672d55bde8c6661ef6ec0833#code  

`CR`:  
0xb4392c63dd85dc6edd43dca9201435437388afc2  
https://ropsten.etherscan.io/address/0xb4392c63dd85dc6edd43dca9201435437388afc2#code  
    
以下对同一个BPhash操作:  
第一次`WareHouse_Admins.compose`:  
`bphash`: "a", `maker`: 0xbd70d89667a3e1bd341ac235259c5f2dde8172a9
https://ropsten.etherscan.io/tx/0x1def319b47d95b88ef6a1b2b9793b914c857c0807214788f211854c707ad515f  

然后`CopyrightCenter.shelf`:
https://ropsten.etherscan.io/tx/0xa136aaf26df53d62726f766af92add338a1a63546a5c58871237a250b5ea59e1  
此时:  
`WareHouse_Admins`的`canDecompose`返回`false`.    
`CopyrightCenter`的`canShelf`也返回`false`, `canUnshelf`返回`true`, `getTokenIdOfCR`返回`1`,   
再去`cr`下面查询`makerOf(1)`, `exists(1)`等结果, 符合条件

接着`CopyrightCenter.unshelf`:
https://ropsten.etherscan.io/tx/0x166cbea1dcdd8f591ced8093d94c36ba7595042d99117b7b1b2bfeef2339a912  
此时:  
`WareHouse_Admins`的`canDecompose`返回`true`
`copyrightcenter`的`canShelf`返回`true`, `canUnshelf`返回`false`, `getTokenIdOfCR`返回`0`(0表示没有初始化, 不存在)

接着又`CopyrightCenter.shelf`:
https://ropsten.etherscan.io/tx/0x5f3dc18a0d230424eb24bc25caaa212b20bfbaff4d0fc9cf543f462c3a82caab  
此时:  
`WareHouse_Admins`的`canDecompose`返回`false`.  
`copyrightcenter`的`canShelf`也返回`false`, `canUnshelf`返回`true`, `getTokenIdOfCR`返回`2`, 
再去cr下面查询makerOf(2), exists(2)等结果, 符合条件


接着再次`CopyrightCenter.unshelf`:
https://ropsten.etherscan.io/tx/0x727d29d1dbea1e98b51e4245dce8a693647fe6150e9d5e691d952c62abfd6297  
此时:  
`WareHouse_Admins`的`canDecompose`返回`true`
`copyrightcenter`的`canShelf`返回`true`, `canUnshelf`返回`false`, `getTokenIdOfCR`返回`0`(0表示没有初始化, 不存在)

最后调用`WareHouse_Admins.deCompose`:  
https://ropsten.etherscan.io/tx/0x0e7a165e1dc13c9289f8f8e116ec534ca47bb20da2d944a90b21e66d3bf8f3d4  
