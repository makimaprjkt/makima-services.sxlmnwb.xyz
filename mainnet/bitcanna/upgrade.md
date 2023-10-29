---
description: Prepare for and the upcomming chain upgrade using Cosmovisor.
---

# Upgrade

<figure><img src="../../.gitbook/assets/bitcanna.svg" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** bitcanna-1 | **Version:** v2.0.3

{% hint style="info" %}
Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.
{% endhint %}

| Executable | Node Version |
| ----| ------------ |
| **bcnad**|v2.0.2 > v2.0.3|

{% hint style="info" %}
if you get an error looking at logs updating on nodes try updating available new version.
{% endhint %}

## Make a stop node
```bash
sudo systemctl stop bcnad
```

## Download and build upgrade binaries

#### Clone project repository
```bash
cd $HOME
rm -rf bcna
git clone https://github.com/BitCannaGlobal/bcna.git
cd bcna
git checkout v2.0.3
```

#### Build binaries
```bash
make build
```

#### Prepare binaries for Cosmovisor
```bash
mkdir -p $HOME/.bcna/cosmovisor/upgrades/wakeandbake/bin
mv build/bcnad $HOME/.bcna/cosmovisor/upgrades/wakeandbake/bin/
rm -rf build
```

#### **Start service and check logs**
```bash
sudo systemctl start bcnad && sudo journalctl -fu bcnad -o cat
```
