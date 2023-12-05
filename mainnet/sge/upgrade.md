---
description: Prepare for and the upcomming chain upgrade using Cosmovisor.
---

# Upgrade

<figure><img src="../../.gitbook/assets/sge.png" height="100" weight="100" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet | **Chain ID:** sgenet-1 | **Version:** v1.1.1

{% hint style="info" %}
Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.
{% endhint %}

| Executable | Node Version |
| ----| ------------ |
| **sged**|v1.1.1 > v1.1.2|

{% hint style="info" %}
if you get an error looking at logs updating on nodes try updating available new version.
{% endhint %}

## Make a stop node
```bash
sudo systemctl stop sged
```

## Download and build upgrade binaries

#### Clone project repository
```bash
cd $HOME
rm -rf sge
git clone https://github.com/sge-network/sge.git
cd sge
git checkout v1.1.2
```

#### Build binaries
```bash
make build
```

#### Prepare binaries for Cosmovisor
```bash
mkdir -p $HOME/.sge/cosmovisor/upgrades/v1.1.2/bin
mv build/sged $HOME/.sge/cosmovisor/upgrades/v1.1.2/bin/
rm -rf build
```

#### **Start service and check logs**
```bash
sudo systemctl start sged && sudo journalctl -fu sged -o cat
```
