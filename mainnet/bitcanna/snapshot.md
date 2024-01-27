---
description: Catch the latest block faster by using our daily snapshots.
---

# Snapshot

<figure><img src="../../.gitbook/assets/bitcanna.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** bitcanna-1 | **Version:** v3.0.0

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
sudo systemctl stop bcnad
cp $HOME/.bcna/data/priv_validator_state.json $HOME/.bcna/priv_validator_state.json.backup
rm -rf $HOME/.bcna/data
```

### Download latest snapshot

```bash
curl -L https://ss-bitcanna.sxlmnwb.xyz/mainnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.bcna
mv $HOME/.bcna/priv_validator_state.json.backup $HOME/.bcna/data/priv_validator_state.json
```

### Restart the service and check the log

```bash
sudo systemctl start bcnad && sudo journalctl -fu bcnad -o cat
```
