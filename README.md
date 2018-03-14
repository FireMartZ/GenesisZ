# GenesisZ
HUSH genesis block mining script. Runs an external miner for finding valid Equihash solutions.
Inspired by [GenesisH0](https://github.com/lhartikk/GenesisH0), but written from scratch.

## Features
- Modify every parameter that influences the block header hash (see _Usage_)
- Sensible defaults
- Placeholders of the form `{BTC}`, `{ETH}` or `{ZEC}` in the `pszTimestamp` input string get translated to the currencie's latest block number and hash. 
- Uses the [`python-zcashlib`](https://github.com/sebastianst/python-zcashlib), which is a (still very much unfinished) extension of the well-known [`python-bitcoinlib`](https://github.com/petertodd/python-bitcoinlib).

## Getting started
Clone this repo, create a **python 3** virtualenv and install dependencies with pip:
```bash
git clone --recursive https://github.com/FireMartZ/GenesisZ
python3 -m venv GenesisZ
cd GenesisZ
source bin/activate
pip install -r requirements.txt
```
Make sure you have a working and supported equihash solver. Currently, only the [silent army](https://github.com/mbevand/silentarmy) solver is supported.

#### python-zcashlib submodule
Note that the zcashlib is used as a submodule, since I haven't uploaded it to PyPI yet (and because it's easier for the current interdependent development). That's why you must use the `--recursive` flag during cloning. When you update this repo, don't forget to update the submodule as well, i.e., run `git pull && git submodule update` to update.

## Examples
#### HUSH
Mine the hush mainnet gensis block by calling (cheating because we know the right nonce):
```bash
./genesis.py -c mainnet -s '../equihash/equi1 -p 34 -s -n 302317566 -r 9 -x' -v -t 1479401611 -b 0x1f07ffff -Z Zdashe540ecf100001889836c7d491a2f44e6bc6076d59e5e317255946b71be3fc516 -p 04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f
```
testnet block:
```bash
./genesis.py -c testnet -s '../equihash/equi1 -p 27 -s -n 5 -r 9 -x' -v -t 1506883264 -b 0x2007ffff -E 0x1f07ffff -Z Zdashe540ecf100001889836c7d491a2f44e6bc6076d59e5e317255946b71be3fc516 -p 04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f
```
regtest block:
```bash
./genesis.py -c regtest -s '../equihash/eq4851 -p 27 -s -n 0000 -r 100000 -x' -v -t 1519568466 -b 0x200f0f0f -E 0x1f07ffff -Z Zdashe540ecf100001889836c7d491a2f44e6bc6076d59e5e317255946b71be3fc516 -p 04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f
```

## Usage
```
usage: genesis.py [-h] [-c {mainnet,testnet,regtest}] [-t TIME] [-C COINNAME]
                  [-z TIMESTAMP] [-Z PSZTIMESTAMP] [-n NONCE] [-p PUBKEY]
                  [-b BITS] [-E EXTRANONCE] [-V VALUE] [-s SOLVER] [-v]

This script uses any Equihash solver to find a solution for the specified
genesis block

optional arguments:
  -h, --help            show this help message and exit
  -c {mainnet,testnet,regtest}, --chainparams {mainnet,testnet,regtest}
                        Select the core chain parameters for PoW limit. May be
                        mainnet, testnet, or regtest
  -t TIME, --time TIME  unix time to set in block header (defaults to current
                        time)
  -C COINNAME, --coinname COINNAME
                        the coin name prepends the blake2s hash of timestamp
                        in pszTimestamp
  -z TIMESTAMP, --timestamp TIMESTAMP
                        the pszTimestamp found in the input coinbase
                        transaction script. Will be blake2s'd and then
                        prefixed by coin name. Default is Zcash's mainnet
                        pszTimestamp. You may use tokens of the form {XYZ},
                        which will be replaced by the current block index and
                        hash of coin XZY (BTC, ETH or ZEC). Always the latest
                        block is retrieved, regardless of time argument.
  -Z PSZTIMESTAMP, --pszTimestamp PSZTIMESTAMP
                        Specify the pszTimestamp directly. Will ignore options
                        -C and -z
  -n NONCE, --nonce NONCE
                        nonce to start with when searching for a valid
                        equihash solution; parsed as hex
  -p PUBKEY, --pubkey PUBKEY
                        the pubkey found in the output transaction script
  -b BITS, --bits BITS  the target in compact representation, defining a
                        difficulty of 1
  -E EXTRANONCE, --extra-nonce EXTRANONCE
                        Usually, the coinbase script contains the nBits as
                        fixed first data, which in bitcoin is also referred to
                        as extra nonce. This conventional behaviour can be
                        changed by specifying this parameter (not recommended
                        for mainnet, useful for testnet).
  -V VALUE, --value VALUE
                        output transaction value in zatoshi (1 ZEC = 100000000
                        zatoshi)
  -s SOLVER, --solver SOLVER
                        silentarmy solver command; must accept the serialized
                        block header in hex(RPC byte order) as argument.
  -v, --verbose         verbose mode

```

## TODO

- [ ] Complete this TODO list
- [ ] Regtest genesis block mining. silentarmy only supports the main/testnet parameters `N, K = 200, 9`. See also this [zcash forum thread](https://forum.z.cash/t/equihash-solver-for-n-k-48-5-other-than-default-200-9).
- [ ] Use solvers' native APIs instead of reading `stdout`
- [ ] more examples (zcash testnet, zclassic)
- [ ] Make block number selectable for the `pszTimestamp` placeholders. Like `{BTC:1234}` for block #1234. 

## License
Released under the GPLv3, see `LICENSE` file.
