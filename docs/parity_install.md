### 1. 安装 Parity

    建议安装稳定版，并在配置文件中设置更新规则，确保关键更新被自动安装

```
# 命令行安装稳定版
bash <(curl https://get.parity.io -L) -r stable

# 命令行安装最新beta版本
bash <(curl https://get.parity.io -L)

```

### 2. 安装服务

    默认情况下系统没有安装parity的相关启停服务，需要手动进行安装配置

```
sudo wget https://raw.githubusercontent.com/paritytech/parity-ethereum/master/scripts/parity.service -O /etc/systemd/system/parity.service

sudo chmod +x /etc/systemd/system/parity.service

sudo systemctl enable parity

```

### 3. 配置文件

    在 /etc/parity 目录中添加配置文件 config.toml 


```
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
bootnodes = []
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
interface = "local"
cors = []
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "traces", "rpc", "shh", "shh_pubsub"]
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
path = "/mnt/parity/jsonrpc.ipc"
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "parity_accounts", "traces", "rpc", "shh", "shh_pubsub"]

[dapps]
disable = false
path = "/mnt/parity/dapps"

[misc]
logging = "own_tx=trace"
log_file = "/var/log/parity.log"
color = true
```