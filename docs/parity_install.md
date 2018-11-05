**[首页](https://docs.workwithme.vip)** > parity 简明安装

### 1. 安装 Parity

    建议安装稳定版，并在配置文件中设置更新规则，确保关键更新被自动安装

```bash
# 命令行安装稳定版
bash <(curl https://get.parity.io -L) -r stable

# 命令行安装最新beta版本
bash <(curl https://get.parity.io -L)

```

### 2. 安装服务

    默认情况下系统没有安装parity的相关启停服务，需要手动进行安装配置

```bash
sudo wget https://raw.githubusercontent.com/paritytech/parity-ethereum/master/scripts/parity.service -O /etc/systemd/system/parity.service

sudo chmod +x /etc/systemd/system/parity.service

sudo systemctl enable parity

```

### 3. 配置文件

    在 /etc/parity 目录中添加配置文件 config.toml 
    配置生成器：https://paritytech.github.io/parity-config-generator/


```bash
# This config should be placed in following path:
#   ~/.local/share/io.parity.ethereum/config.toml

[parity]
# Blockchain and settings will be stored in /mnt/parity.
mode = "last"
mode_timeout = 300
mode_alarm = 3600
auto_update = "critical"
release_track = "current"
public_node = false
no_download = false
no_consensus = false
no_persistent_txqueue = false


chain = "foundation"
base_path = "/data/parity"
db_path = "/data/parity/db-path"
keys_path = "/data/parity/keys-path"
identity = ""
light = false


[network]
# Parity will maintain at most 100 peers.
port = 31313
min_peers = 25
max_peers = 100
nat = "any"
id = 1
# bootnodes = []  # 该参数需要移除
discovery = true
warp = true
allow_ips = "all"
snapshot_peers = 0
max_pending_peers = 64
no_serve_light = false

reserved_only = false

[rpc]
# JSON-RPC over HTTP will be accessible on port 9988.
port = 9988
disable = false
interface = "0.0.0.0"
# Allows Cross-Origin Requests from domain '*,'.
cors = ["*", ""]
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "traces", "rpc", "shh", "shh_pubsub", "personal"]
hosts = ["none"]

[websockets]
# JSON-RPC over WebSockets will be accessible on port 9989.
port = 9989
disable = false
interface = "local"
origins = ["none"]
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "traces", "rpc", "shh", "shh_pubsub"]
hosts = ["none"]

[ipc]
disable = false
path = "/data/parity/jsonrpc.ipc"
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "parity_accounts", "traces", "rpc", "shh", "shh_pubsub"]

[dapps]
disable = false
path = "/data/parity/dapps"

[misc]
logging = "own_tx=trace"
log_file = "/var/log/parity.log"
color = true
```


### 4. 启动服务

```bash
sudo systemctl start parity
```

### 5. 访问命令控制台

    访问命令控制台的前提是该机器安装了geth钱包，相关安装方式这里不再描述

```bash
geth attach http://127.0.0.1:9988  # 端口号可在配置文件中修改，修改后需要重启服务
```

### 6. 使用 rpc 协议访问 parity 接口

```bash
# 查看当前的节点数量

curl --data '{"method":"net_peerCount","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:9988

# 查看当前的同步状态

curl --data '{"method":"eth_syncing","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:9988
```
