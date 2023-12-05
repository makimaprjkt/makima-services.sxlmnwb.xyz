# 🟢 SGE

<figure><img src="../../.gitbook/assets/sge.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** sgenet-1 | **Version:** v1.1.1

{% hint style="info" %}
SIX SIGMA SPORTS combines the best of DeFi innovation and COSMOS scalability to create a user-driven marketplace for sports betting.
{% endhint %}

[**Website**](https://sixsigmasports.io) | [**Discord**](https://discord.gg/9E6dqDNRrW) | [**X**](https://twitter.com/SixSigmaSports)

## **Public Endpoints**

* RPC : [rpc-sge.sxlmnwb.xyz](https://rpc-sge.sxlmnwb.xyz)
* API : [api-sge.sxlmnwb.xyz](https://api-sge.sxlmnwb.xyz)
* gRPC : `grpc-sge.sxlmnwb.xyz:11490`

## Peering

#### **State Sync Peer**
```
f37ec216380fe3cf2cb192cba93e73e9b2e6ed1c@rpc-sge.sxlmnwb.xyz:11456
```

#### **Seeds**
```
39d10a2e6ba7acbd714d1fde9ba0f2f4b7346f67@seed-sge.sxlmnwb.xyz:11456
```

#### **Addrbook**
```bash
curl -Ls https://ss-sge.sxlmnwb.xyz/mainnet/addrbook.json > $HOME/.sge/config/addrbook.json
```

#### **Live Peers**
```bash
PEERS="$(curl -sS https://rpc-sge.sxlmnwb.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.sge/config/config.toml
```
