Step by Step
===

https://docs.binance.org/atomic-swap.html#swap-tokens-from-ethereum-to-binance-chain

# Let's Do This

https://docs.binance.org/atomic-swap.html#workflows

# Concept

Asset must available on Ethereum (ERC-20) or binance chain (BEP2)

# Steps

1. ERC-20 contract (PPC) -> approve volume to Swap Contract
1. Swap contract -> HTLT
1. Deputy fetch block from ethereum & binance chain
1. Deputy tx with matched (Swap contract) address
1. Deputy send HTLT to binance chain
1. Use tbnbcli to fetch HTLT swap-id
1. Use tbnbcli to claim token (PPC) on binance chain

### Deploy Smart Contract

[Deploy smart contract to ropsten](https://medium.com/coinmonks/5-minute-guide-to-deploying-smart-contracts-with-truffle-and-ropsten-b3e30d5ee1e)

Get MNEMONIC from metamesk

GET ropsten.infura.io API_KEY from infura project
https://infura.io/stats/cd9643b1870b489b93477921cb767882

Remix deploy

Etherscan verify

### Deploy bep3 deputy

Get deputy
```
go get github.com/binance-chain/bep3-deputy

cd ${GOPATH}/src/github.com/binance-chain/bep3-deputy
```

build
```
export GO111MODULE=on
go mod download
make build
```

Edit config

```
vim config/config.json
```

[Binance seed](https://docs.binance.org/api-reference/api-server.html#example-for-testnet)

```
./deputy --bnb-network 0 --config-type local --config-path ../config/config.json
```

Use screen to run background

```
screen

Ctrl+A+D
```

### ERC-20 contract Approve

[ERC-20 Contract -> Approve](https://ropsten.etherscan.io/address/0xd93395b2771914e1679155f3ea58c41d89d96098#writeContract)

```
_spender (address)
0xA08E0F38462eCd107adE62Ee3004850f2448f3d6

_value (uint256)
1000000000000000000000
100^18
```

### Swap contract HTLT

[Swap Contract -> Write Contract](https://ropsten.etherscan.io/address/0xA08E0F38462eCd107adE62Ee3004850f2448f3d6#writeContract)

```
_randomNumberHash (bytes32)
0xa665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae9

_timestamp (uint64)
1570094652

_heightSpan (uint256)
123

_recipientAddr (address)
0x938a452d293c23C2CDEae0Bf01760D8ECC4F826b

_bep2SenderAddr (bytes20)
0x0

_bep2RecipientAddr (bytes20)
0xee82ca237387495e9603f4d8d7efed128bdd59a6

_outAmount (uint256)
100000000000000000000
100^18

_bep2Amount (uint256)
1
```

[Decode bech32 address](https://slowli.github.io/bech32-buffer/)

[Get unix epoch time](https://www.epochconverter.com/)

Write

### Check

https://ropsten.etherscan.io/address/0xa08e0f38462ecd107ade62ee3004850f2448f3d6

### Check deputy

```
2019-10-03 09:25:13 DEBUG ParseHTLTEvent sender addr: 0x938a452d293c23C2CDEae0Bf01760D8ECC4F826b
2019-10-03 09:25:13 DEBUG ParseHTLTEvent receiver addr: 0x938a452d293c23C2CDEae0Bf01760D8ECC4F826b
2019-10-03 09:25:13 DEBUG ParseHTLTEvent swap id: fbf14aad24d124101838813a172258313be9e0e885603a967ba8563355e8f60e
2019-10-03 09:25:13 DEBUG ParseHTLTEvent random number hash: a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae9
2019-10-03 09:25:13 DEBUG ParseHTLTEvent bep2 addr: ee82ca237387495e9603f4d8d7efed128bdd59a6
2019-10-03 09:25:13 DEBUG ParseHTLTEvent timestamp: 1570094652
2019-10-03 09:25:13 DEBUG ParseHTLTEvent expire height: 6496162
2019-10-03 09:25:13 DEBUG ParseHTLTEvent erc20 amount: 100000000000000000000
2019-10-03 09:25:13 DEBUG ParseHTLTEvent bep2 amount: 1
```

# Get HTLT with tbnbcli

[Get tbnbcli](https://docs.binance.org/api-reference/cli.html#command-line-interface-cli)

```
./cli/testnet/0.5.8.1/mac/tbnbcli

./cli/testnet/0.5.8.1/mac/tbnbcli token HTLT query-swapIDs-by-recipient  --recipient-addr tbnb1a6pv5gmnsay4a9sr7nvd0mldz29a6kdxye37ce --chain-id Binance-Chain-Nile --trust-node --node http://data-seed-pre-0-s3.binance.org:80

ERROR: unknown flag: --recipient-addr
```

Don't need to fetch swap id. Deputy got it

```
./cli/testnet/0.5.8.1/mac/tbnbcli token claim \
  --swap-id fbf14aad24d124101838813a172258313be9e0e885603a967ba8563355e8f60e \
  --random-number a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae9 \
  --from ~/Downloads/37554311-0115-44a7-ac76-08551672bf64_keystore.txt \
  --chain-id Binance-Chain-Nile \
  --trust-node \
  --node http://data-seed-pre-0-s3.binance.org:80

ERROR: unknown flag: --swap-id
```

The end.
