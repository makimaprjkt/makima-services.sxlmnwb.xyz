---
description: Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.
---

# Installation

<figure><img src="../../.gitbook/assets/bitcanna.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** bitcanna-1 | **Version:** v3.0.0

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
curl -Ls https://go.dev/dl/go1.21.5.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

## Download and build binaries

#### Clone project repository
```bash
cd $HOME
rm -rf bcna
git clone https://github.com/BitCannaGlobal/bcna.git
cd bcna
git checkout v3.0.0
```

#### Build binaries
```bash
make build
```

#### Prepare binaries for Cosmovisor
```bash
mkdir -p $HOME/.bcna/cosmovisor/genesis/bin
mv build/bcnad $HOME/.bcna/cosmovisor/genesis/bin/
rm -rf build
```

#### Create application symlinks
```bash
sudo ln -s $HOME/.bcna/cosmovisor/genesis $HOME/.bcna/cosmovisor/current -f
sudo ln -s $HOME/.bcna/cosmovisor/current/bin/bcnad /usr/local/bin/bcnad -f
```

## Install Cosmovisor and create a service

#### Download and install Cosmovisor
```bash
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

#### Create service
```bash
sudo tee /etc/systemd/system/bcnad.service > /dev/null << EOF
[Unit]
Description=Bitcanna Node Service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.bcna"
Environment="DAEMON_NAME=bcnad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.bcna/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```

## Register service
```bash
sudo systemctl daemon-reload
sudo systemctl enable bcnad
```

## Initialize the node

#### Set node configuration
```bash
bcnad config chain-id bitcanna-1
bcnad config keyring-backend file
bcnad config node tcp://localhost:11357
```

#### Initialize the node
```bash
bcnad init $MONIKER --chain-id bitcanna-1
```

#### Download genesis and addrbook
```bash
curl -Ls https://ss-bitcanna.sxlmnwb.xyz/mainnet/genesis.json > $HOME/.bcna/config/genesis.json
curl -Ls https://ss-bitcanna.sxlmnwb.xyz/mainnet/addrbook.json > $HOME/.bcna/config/addrbook.json
```

#### Set peers and seeds
```bash
PEERS="1bde35502e91374b5c06b843964c56050455aaef@rpc-bitcanna.sxlmnwb.xyz:11356"
SEEDS="39d10a2e6ba7acbd714d1fde9ba0f2f4b7346f67@seed-bitcanna.sxlmnwb.xyz:11356"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.bcna/config/config.toml
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|" $HOME/.bcna/config/config.toml
```

#### Set minimum gas price
```bash
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0ubcna\"|" $HOME/.bcna/config/app.toml
```

#### Set pruning
```bash
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.bcna/config/app.toml
```

#### Set custom ports
```bash
CUSTOM_CUSTOM_PORT=113
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.bcna/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.bcna/config/app.toml
```

## Download latest chain snapshot
```bash
curl -L https://ss-bitcanna.sxlmnwb.xyz/mainnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.bcna
[[ -f $HOME/.bcna/data/upgrade-info.json ]] && cp $HOME/.bcna/data/upgrade-info.json $HOME/.bcna/cosmovisor/genesis/upgrade-info.json
```

## Start service 

```bash
sudo systemctl start bcnad
```

#### Check service logs
```bash
sudo journalctl -fu bcnad -o cat
```
