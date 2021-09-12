### On Kichain Server:
```nano /root/kichain/kid/config/config.toml```
Check the rpc laddr section.
If 127.0.0.1:26657 change to 0.0.0.0:26657

Restart kichain service to apply
```
systemctl restart kichaind.service
```

# Install Cosmos Relayer
### On Umee Server:
```
git clone https://github.com/cosmos/relayer.git
cd relayer
git checkout v0.9.3
make install
```

## Check relay ver
```
rly version
```
> version: v0.9.3
> commit: 4b81fa59055e3e94520bdfae1debe2fe0b747dc1
> cosmos-sdk: v0.42.4
> go: go1.15.11 linux/amd64


## Init rly config
```
rly config init
```

## Add chain configs
Edit config.yaml (put your kichain node IP)

```nano $HOME/.relayer/configs/config.yaml```

```
global:
  api-listen-addr: :5183
  timeout: 3m
  light-cache-size: 20
chains:
- key:
  chain-id: umee-betanet-1
  rpc-addr: http://127.0.0.1:26657
  account-prefix: umee
  gas-adjustment: 1.5
  gas-prices: 0.025uumee
  trusting-period: 10m
- key:
  chain-id: kichain-t-4
  rpc-addr: http://xx.xx.xx.xx:26657
  account-prefix: tki
  gas-adjustment: 1.5
  gas-prices: 0.025utki
  trusting-period: 10m
paths: {}
```

# ADD WALLETS
## Add umee key
```
rly keys add umee-betanet-1 umeek
```

## Restore kichain key
```
rly keys restore kichain-t-4 kichk "mnemonics"
```

## Add keys to chain
```
rly chains edit umee-betanet-1 key umeek
rly chains edit kichain-t-4 key kichk
```

# Fund with tokens

### umeed tx bank send <FROM_key_or_address> <TO_umee_address_relayer> <AMOUNT>uumee --chain-id umee-betanet-1 --gas=auto --fees=1000uume
```
umeed tx bank send $UMEE_NODE_WALLET umee1zhnu22rczd6ptz98xsm9eunf8rw8kq2slt4sgq 1000000uumee --chain-id umee-betanet-1 --gas=auto --fees=1000uumee
```

# Check balance
```
rly q balance umee-betanet-1
rly q bal kichain-t-4
```

# Init light client
```
rly light init umee-betanet-1 -f
rly light init kichain-t-4 -f
```

# Generate path Umee to Kichain
```
rly paths generate umee-betanet-1 kichain-t-4 umee_ki --port=transfer
```
  
# Check preconfig
```
rly chains list
```

# Open TX link
```
rly tx link umee_ki
```

# Send tokens Umee to Kichain
```
rly tx transfer umee-betanet-1 kichain-t-4 1000000uumee tki1a4zkyz85wlrzauc87msjk595zq9ad0htzg4wfs --path umee_ki
```
  
# Generate path Kichain to Umee
```
rly paths generate kichain-t-4 umee-betanet-1 ki_umee --port=transfer
```
> I[2021-09-11|17:03:41.056] ★ Channel created: [kichain-t-4]chan{channel-181}port{transfer} -> [umee-betanet-1]chan{channel-17}port{transfer} 
  
# Open Channel KI to UMEE
```
rly tx link ki_umee
```

# Send Tokens Umee to Kichain
```
rly tx transfer kichain-t-4 umee-betanet-1 100000utki umee1zhnu22rczd6ptz98xsm9eunf8rw8kq2slt4sgq --path ki_umee
```
