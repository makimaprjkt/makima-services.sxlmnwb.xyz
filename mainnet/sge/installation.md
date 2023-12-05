---
description: Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.
---

# Installation

<figure><img src="../../.gitbook/assets/sge.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** sgenet-1 | **Version:** v1.1.1

### Setup validator name

{% hint style='info' %}
Replace **YOUR_MONIKER_GOES_HERE** with your validator name
{% endhint %}

```bash
MONIKER="YOUR_MONIKER_GOES_HERE"
```

## Install dependencies

#### Update system and install build tools

```bash
sudo apt-get update -y
sudo apt-get install make build-essential gcc git jq chrony lz4 -y
```

## Install Go (Golang)

```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.19.5.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

## Download and build binaries

#### Clone project repository
```bash
cd $HOME
rm -rf sge
git clone https://github.com/sge-network/sge.git
cd sge
git checkout v1.1.1
```

#### Build binaries
```bash
make build
```

#### Prepare binaries for Cosmovisor
```bash
mkdir -p $HOME/.sge/cosmovisor/genesis/bin
mv build/sged $HOME/.sge/cosmovisor/genesis/bin/
rm -rf build
```

#### Create application symlinks
```bash
sudo ln -s $HOME/.sge/cosmovisor/genesis $HOME/.sge/cosmovisor/current -f
sudo ln -s $HOME/.sge/cosmovisor/current/bin/sged /usr/local/bin/sged -f
```

## Install Cosmovisor and create a service

#### Download and install Cosmovisor
```bash
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
```

#### Create service
```bash
sudo tee /etc/systemd/system/sged.service > /dev/null << EOF
[Unit]
Description=SGE Node Service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.sge"
Environment="DAEMON_NAME=sged"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.sge/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```

## Register service
```bash
sudo systemctl daemon-reload
sudo systemctl enable sged
```

## Initialize the node

#### Set node configuration
```bash
sged config chain-id sgenet-1
sged config keyring-backend file
sged config node tcp://localhost:11457
```

#### Initialize the node
```bash
sged init $MONIKER --chain-id sgenet-1
```

#### Download genesis and addrbook
```bash
curl -Ls https://ss-sge.sxlmnwb.xyz/mainnet/genesis.json > $HOME/.sge/config/genesis.json
curl -Ls https://ss-sge.sxlmnwb.xyz/mainnet/addrbook.json > $HOME/.sge/config/addrbook.json
```

#### Set peers and seeds
```bash
PEERS="f37ec216380fe3cf2cb192cba93e73e9b2e6ed1c@rpc-sge.sxlmnwb.xyz:11456"
SEEDS="39d10a2e6ba7acbd714d1fde9ba0f2f4b7346f67@seed-sge.sxlmnwb.xyz:11456"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.sge/config/config.toml
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|" $HOME/.sge/config/config.toml
```

#### Set minimum gas price
```bash
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0usge\"|" $HOME/.sge/config/app.toml
```

#### Set pruning
```bash
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.sge/config/app.toml
```

#### Set custom ports
```bash
CUSTOM_CUSTOM_PORT=114
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.sge/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.sge/config/app.toml
```

## Download latest chain snapshot
```bash
curl -L https://ss-sge.sxlmnwb.xyz/mainnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.sge
[[ -f $HOME/.sge/data/upgrade-info.json ]] && cp $HOME/.sge/data/upgrade-info.json $HOME/.sge/cosmovisor/genesis/upgrade-info.json
```

## Start service 

```bash
sudo systemctl start sged
```

#### Check service logs
```bash
sudo journalctl -fu sged -o cat
```
