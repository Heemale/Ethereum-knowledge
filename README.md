## 搭建本地以太坊pow私有链

使用puppeth工具
```bash
D:\Test-8545\private>puppeth
+-----------------------------------------------------------+
| Welcome to puppeth, your Ethereum private network manager |
|                                                           |
| This tool lets you create a new Ethereum network down to  |
| the genesis block, bootnodes, miners and ethstats servers |
| without the hassle that it would normally entail.         |
|                                                           |
| Puppeth uses SSH to dial in to remote servers, and builds |
| its network components out of Docker containers using the |
| docker-compose toolset.                                   |
+-----------------------------------------------------------+

Please specify a network name to administer (no spaces, hyphens or capital letters please)
> chainskills

Sweet, you can set this via --network=chainskills next time!

[32mINFO [0m[11-30|20:47:15.850] Administering Ethereum network           [32mname[0m=chainskills
[33mWARN [0m[11-30|20:47:16.164] No previous configurations found         [33mpath[0m=.puppeth\chainskills
```

配置生成创世纪文件chainskills.json
```bash
What would you like to do? (default = stats)

 1. Show network stats
 2. Configure new genesis
 3. Track new remote server
 4. Deploy network components
> 2

What would you like to do? (default = create)
 1. Create new genesis from scratch
 2. Import already existing genesis
> 1
```

选择共识机制
```bash
Which consensus engine to use? (default = clique)
 1. Ethash - proof-of-work
 2. Clique - proof-of-authority
> 1

Which accounts should be pre-funded? (advisable at least one)
> 0x

Should the precompile-addresses (0x1 .. 0xff) be pre-funded with 1 wei? (advisable yes)
>
```

设置chainID
```bash
Specify your chain/network ID if you want an explicit one (default = random)

> 4224
> [32mINFO [0m[11-30|20:47:53.728] Configured new genesis block

What would you like to do? (default = stats)
 1. Show network stats
 2. Manage existing genesis
 3. Track new remote server
 4. Deploy network components
> 2
```

导出创世纪文件
```bash
 1. Modify existing configurations
 2. Export genesis configurations
 3. Remove genesis configuration
> 2

Which folder to save the genesis specs into? (default = current)
  Will create chainskills.json, chainskills-aleth.json, chainskills-harmony.json, chainskills-parity.json
>[32mINFO [0m[11-30|20:48:34.246] Saved native genesis chain spec          [32mpath[0m=chainskills.json
>[32mINFO [0m[11-30|20:48:34.252] Saved genesis chain spec                 [32mclient[0m=aleth [32mpath[0m=chainskills-aleth.json
>[32mINFO [0m[11-30|20:48:34.257] Saved genesis chain spec                 [32mclient[0m=parity [32mpath[0m=chainskills-parity.json
>[32mINFO [0m[11-30|20:48:34.263] Saved genesis chain spec                 [32mclient[0m=harmony [32mpath[0m=chainskills-harmony.json

What would you like to do? (default = stats)
 1. Show network stats
 2. Manage existing genesis
 3. Track new remote server
 4. Deploy network components
 
^c
```

初始化创世纪文件

```bash
D:\Test-8545\private>geth --datadir . init chainskills.json
INFO [11-30|20:50:18.800] Maximum peer count                       ETH=50 LES=0 total=50
INFO [11-30|20:50:18.870] Set global gas cap                       cap=25000000
INFO [11-30|20:50:18.874] Allocated cache and file handles         database=D:\Test-8545\private\geth\chaindata cache=16.00MiB handles=16
INFO [11-30|20:50:19.296] Writing custom genesis block
INFO [11-30|20:50:19.303] Persisted trie from memory database      nodes=354 size=50.23KiB time="975µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-30|20:50:19.318] Successfully wrote genesis state         database=chaindata                           hash="be770e…95d9c2"
INFO [11-30|20:50:19.325] Allocated cache and file handles         database=D:\Test-8545\private\geth\lightchaindata cache=16.00MiB handles=16
INFO [11-30|20:50:19.413] Writing custom genesis block
INFO [11-30|20:50:19.418] Persisted trie from memory database      nodes=354 size=50.23KiB time=1.0101ms gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-30|20:50:19.434] Successfully wrote genesis state         database=lightchaindata                           hash="be770e…95d9c2"
```

创建两个账户
```bash
D:\Test-8545\private>geth --datadir . account new
INFO [11-30|20:52:13.236] Maximum peer count                       ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0xAa6cd61c58F679b6dB9eE432925aE06Ca6798001
Path of the secret key file: keystore\UTC--2020-11-30T12-52-20.321058300Z--aa6cd61c58f679b6db9ee432925ae06ca6798001

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!

D:\Test-8545\private>geth --datadir . account new
INFO [11-30|20:52:34.483] Maximum peer count                       ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0x1bb8d4f0f2Bcf86B24A035F88fF12c557c5a83c9
Path of the secret key file: keystore\UTC--2020-11-30T12-52-41.496669200Z--1bb8d4f0f2bcf86b24a035f88ff12c557c5a83c9

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

挖矿命令：写入start_geth_attach.bat文件
```shell
geth --networkid 4224 --mine --miner.threads 3 --syncmode full --datadir "." --nodiscover --http --http.port "8545" --ws --ws.port "8546" --port "30303" --http.corsdomain "*" --nat "any" --http.api eth,web3,personal,net --allow-insecure-unlock --unlock 0,1,2 --password ./password.sec
```

进入geth控制台命令：写入startnode.cmd文件
```shell
geth attach ipc:\\.\pipe\geth.ipc
```

账户密码：写入password.sec文件
```text
your password
```

geth控制台部分命令：
```bash
D:\Test-8545\private>geth attach ipc:\\.\pipe\geth.ipc
Welcome to the Geth JavaScript console!

instance: Geth/v1.9.23-stable-8c2f2715/windows-amd64/go1.15
coinbase: 0xaa6cd61c58f679b6db9ee432925ae06ca6798001
at block: 32 (Mon Nov 30 2020 20:56:00 GMT+0800 (CST))
 datadir: D:\Test-8545\private
 modules: admin:1.0 debug:1.0 eth:1.0 ethash:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> eth.accounts
["0xaa6cd61c58f679b6db9ee432925ae06ca6798001", "0x1bb8d4f0f2bcf86b24a035f88ff12c557c5a83c9"]
> eth.coinbase
"0xaa6cd61c58f679b6db9ee432925ae06ca6798001"
> eth.getBalance(eth.accounts[0])
150000000000000000000
> eth.getBalance(eth.accounts[1])
0
> eth.sendTransaction({from:"0xaa6cd61c58f679b6db9ee432925ae06ca6798001",to:"0x1bb8d4f0f2bcf86b24a035f88ff12c557c5a83c9",value:web3.toWei(23,'ether')})
"0xca187e664b6d4458ab190f26faa4e7fd50d2dbf87d0d2b4bc0a4947cff9da1f2"
> eth.getBalance(eth.accounts[1])

23000000000000000000
> eth.getBalance(eth.accounts[0])

245000000000000000000
```

目录结构图
> ```bash
> D:\Test-8545\private>tree /f
> 卷 DATA 的文件夹 PATH 列表
> 卷序列号为 2805-0DA1
> D:.
> │  chainskills-aleth.json
> │  chainskills-harmony.json
> │  chainskills-parity.json
> │  chainskills.json
> │  password.sec
> │  startnode.cmd
> │  start_geth_attach.bat
> │
> ├─.puppeth
> │      chainskills
> │
> ├─geth
> │  │  LOCK
> │  │  nodekey
> │  │  transactions.rlp
> │  │
> │  ├─chaindata
> │  │  │  000002.ldb
> │  │  │  000003.log
> │  │  │  CURRENT
> │  │  │  CURRENT.bak
> │  │  │  LOCK
> │  │  │  LOG
> │  │  │  MANIFEST-000004
> │  │  │
> │  │  └─ancient
> │  │          bodies.0000.cdat
> │  │          bodies.cidx
> │  │          diffs.0000.rdat
> │  │          diffs.ridx
> │  │          FLOCK
> │  │          hashes.0000.rdat
> │  │          hashes.ridx
> │  │          headers.0000.cdat
> │  │          headers.cidx
> │  │          receipts.0000.cdat
> │  │          receipts.cidx
> │  │
> │  ├─lightchaindata
> │  │      000001.log
> │  │      CURRENT
> │  │      LOCK
> │  │      LOG
> │  │      MANIFEST-000000
> │  │
> │  └─nodes
> │          000001.log
> │          CURRENT
> │          LOCK
> │          LOG
> │          MANIFEST-000000
> │
> └─keystore
>      UTC--2020-11-30T12-52-20.321058300Z--aa6cd61c58f679b6db9ee432925ae06ca6798001
>      UTC--2020-11-30T12-52-41.496669200Z--1bb8d4f0f2bcf86b24a035f88ff12c557c5a83c9
> ```
>
> 
