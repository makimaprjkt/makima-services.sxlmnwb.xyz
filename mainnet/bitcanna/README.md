# 🟢 BitCanna

<figure><img src="../../.gitbook/assets/bitcanna.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** bitcanna-1 | **Version:** v2.0.3

{% hint style="info" %}
BitCanna will provide a decentralized payment network and supply chain for the legal cannabis industry. On this website, you'll currently find everything related to our blockchain network.
{% endhint %}

[**Website**](https://www.bitcanna.io) | [**Discord**](https://discord.gg/97wUcHqxxE) | [**X**](https://twitter.com/BitcannaGlobal)

## **Public Endpoints**

* RPC : [rpc-bitcanna.sxlmnwb.xyz](https://rpc-bitcanna.sxlmnwb.xyz)
* API : [api-bitcanna.sxlmnwb.xyz](https://api-bitcanna.sxlmnwb.xyz)
* gRPC : `https://grpc-bitcanna.sxlmnwb.xyz:443`

## Peering

#### **State Sync Peer**
```
1bde35502e91374b5c06b843964c56050455aaef@peer-bitcanna.sxlmnwb.xyz:11356
```

#### **Seeds**
```
39d10a2e6ba7acbd714d1fde9ba0f2f4b7346f67@seed-bitcanna.sxlmnwb.xyz:11356
```

#### **Addrbook**
```bash
curl -Ls https://ss-bitcanna.sxlmnwb.xyz/mainnet/addrbook.json > $HOME/.bcna/config/addrbook.json
```

#### **Live Peers**
```bash
PEERS="$(curl -sS https://rpc-bitcanna.sxlmnwb.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.bcna/config/config.toml
```
