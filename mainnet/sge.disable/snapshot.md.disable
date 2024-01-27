---
description: Catch the latest block faster by using our daily snapshots.
---

# Snapshot

<figure><img src="../../.gitbook/assets/sge.png" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** sgenet-1 | **Version:** v1.1.1

{% hint style='info' %}
Snapshots allows a new node to join the network by recovering application state from a backup file. 
Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, 
snapshot server is periodically beeing state-synced.
{% endhint %}

{% hint style='waring' %}
Snapshots are taken automatically every day starting at **00:00 UTC**
{% endhint %}

## Instructions

### Stop the service and reset the data

```bash
sudo systemctl stop sged
cp $HOME/.sge/data/priv_validator_state.json $HOME/.sge/priv_validator_state.json.backup
rm -rf $HOME/.sge/data
```

### Download latest snapshot

```bash
curl -L https://ss-sge.sxlmnwb.xyz/mainnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.sge
mv $HOME/.sge/priv_validator_state.json.backup $HOME/.sge/data/priv_validator_state.json
```

### Restart the service and check the log

```bash
sudo systemctl start sged && sudo journalctl -fu sged -o cat
```
