# Go Crypto
A distributed blockchain cryptocurrency implementation, with a CLI interface.

## Installation
1. Ensure that you have [Go](https://golang.org/doc/install) installed on your machine
2. Clone this repository, then, in the project directory, run:
```sh
$ go build
```
3. You can execute the binary by running:
```sh
$ ./go-crypto
```
> Note: Running `go-crypto` with no arguments will print usage instructions to `stdout`.

## CLI Usage
The "distributed" nature of this blockchain is to run many nodes on a single machine, by specifying port numbers as node IDs. You can specify the port number/node ID in a given terminal session by setting the `$NODE_ID` environment variable to your port number of choice.

### `createblockchain -address <address>`
Create a new blockchain (genesis block), and send the genesis block reward to `<address>`.

### `createwallet`
Generate a new wallet key pair, and save it into a corresponding `wallet.dat` file. Outputs the address of the new wallet to `stdout`.

### `getbalance -address <address>`
Print the wallet balance for `<address>` to `stdout`.

### `listaddresses`
Print all addresses in any `wallet.dat` files to `stdout`.

### `printchain`
Print all blocks in the blockchain to `stdout`.

### `reindexutxo`
Rebuilds the UTXO set.

### `send -from <address> -to <address> -amount <amount>[ -mine]`
Send `<amount>` of coins from address to address. When `-mine` is used, mine on the current node.

### `startnode[ -miner <address>]`
Start a node with an ID specified in `$NODE_ID` environment variable. Optionally specify a `-miner` address.

## Example
Follow the below instructions to instantiate a new blockchain on your machine, with 3 nodes - a central node, a wallet node, and a miner node.
1. Set `$NODE_ID` in the current terminal session (**NODE 3000**):
```sh
$ export NODE_ID=3000
```
2. On **NODE 3000**, create a new wallet, to be used as the central node:
```sh
$ ./go-crypto createwallet
Your new address: 16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ
```
3. On **NODE 3000**, create a new blockchain:
```sh
$ ./go-crypto createblockchain -address 16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ
```
4. Save the genesis block, making it available to other nodes:
```sh
$ cp blockchain_3000.db blockchain_genesis.db
```
5. Open an additional terminal session (**NODE 3001**), and set `$NODE_ID` to a different port number from your central node, to be used as a wallet node:
```sh
export NODE_ID=3001
```
6. On **NODE 3001**, generate three new wallets:
```sh
$ ./go-crypto createwallet
Your new address: 18ZpjvATd4xtra5x2QMkWmihj729BRbt6p

$ ./go-crypto createwallet
Your new address: 1GeGo7buaqo71yfCQ9NPwGFZJM1AVosteV

$ ./go-crypto createwallet
Your new address: 1Gf3ZQKc1VEouPWihSM5CimhZ7eYzbK1bC
```
7. On **NODE 3000**, send some coins to wallet addresses:
```sh
$ ./go-crypto send -from 16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ -to 18ZpjvATd4xtra5x2QMkWmihj729BRbt6p -amount 10 -mine
fe5002a6cbd66c10dceb21d98eb0b81a1d8e09a6f253c6eca7e0a84bb8e8675f

Success!

$ ./go-crypto send -from 16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ -to 1GeGo7buaqo71yfCQ9NPwGFZJM1AVosteV -amount 10 -mine
c239d7ea5933613238efb2ad300107bc6ee05d10d95192f11e75f9d5fab1753f

Success!
```
8. On **NODE 3000**, start the node, and keep it running for the remainder of this example:
```sh
$ ./go-crypto startnode
Starting node 3000

```
9. On **NODE 3001**, start the node's blockchain with the same genesis block as the central node:
```sh
$ cp blockchain_genesis.db blockchain_3001.db
```
10. Start **NODE 3001**:
```sh
$ ./go-crypto startnode
Starting node 3001
Received version command
Received inv command
Recevied inventory with 3 block
Received block command
Recevied a new block!
Added block 00000cbe359cc0de4aadd87351766ca20bc0ef357b2ffa6b8c146e0de17e3621
Received block command
Recevied a new block!
Added block 0000755097a897556bb23ba856e1ff605206979b4c8d6bd1446615592866c70c
Received block command
Recevied a new block!
Added block 00003014666abc9c04a9ac8a8668d1fca221fadd285ae67d12326ba315e6c472
```
11. Check that everything's ok by stopping **NODE 3001** and checking the balances:
```sh
$ ./go-crypto getbalance -address 18ZpjvATd4xtra5x2QMkWmihj729BRbt6p
Balance of '18ZpjvATd4xtra5x2QMkWmihj729BRbt6p': 10

$ ./go-crypto getbalance -address 1GeGo7buaqo71yfCQ9NPwGFZJM1AVosteV
Balance of '1GeGo7buaqo71yfCQ9NPwGFZJM1AVosteV': 10
```
12. On **NODE 3001**, check the balance of the central node, proving that **NODE 3001** has the full blockchain:
```sh
$ ./go-crypto getbalance -address 16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ
Balance of '16uJjnDXDC6NaykibKbw76pfbfqgsUirYZ': 10
```
13. Open an additional terminal session (**NODE 3002**), and set `$NODE_ID` to a different port number from both your central node and wallet node, to be used as a miner node. Then, initialize the blockchain, create a wallet, and start **NODE 3002**:
```sh
$ export NODE_ID=3002
$ cp blockchain_genesis.db blockchain_3002.db
$ ./go-crypto createwallet
Your new address: 17bmRV6jrjitenXt2QfAo33qrEwKrs1sLZ

$ ./go-crypto startnode -miner 17bmRV6jrjitenXt2QfAo33qrEwKrs1sLZ
Starting node 3002
Mining is on. Address to receive rewards:  17bmRV6jrjitenXt2QfAo33qrEwKrs1sLZ
Received version command
Received inv command
Recevied inventory with 3 block
Received block command
Recevied a new block!
Added block 00000cbe359cc0de4aadd87351766ca20bc0ef357b2ffa6b8c146e0de17e3621
Received block command
Recevied a new block!
Added block 0000755097a897556bb23ba856e1ff605206979b4c8d6bd1446615592866c70c
Received block command
Recevied a new block!
Added block 00003014666abc9c04a9ac8a8668d1fca221fadd285ae67d12326ba315e6c472
```
14. On **NODE 3001**, complete some transactions, and observe the output on **NODE 3002**:
```sh
# NODE 3001
$ ./go-crypto send -from 18ZpjvATd4xtra5x2QMkWmihj729BRbt6p -to 1Gf3ZQKc1VEouPWihSM5CimhZ7eYzbK1bC -amount 1
Success!

$ ./go-crypto send -from 1GeGo7buaqo71yfCQ9NPwGFZJM1AVosteV -to 17bmRV6jrjitenXt2QfAo33qrEwKrs1sLZ -amount 1
Success!

# NODE 3002
Received inv command
Recevied inventory with 1 tx
Received tx command
Received inv command
Recevied inventory with 1 tx
Received tx command
e936a00eee5c0f66c6fe250b2d53bcb77463d6ea9cedb754981718960c29fa45

New block is mined!
Received getdata command

```
15. Start **NODE 3001** and observe it download the newly mined block:
```sh
$ ./go-crypto startnode
Starting node 3001
Received version command
Received inv command
Recevied inventory with 4 block
Received block command
Recevied a new block!
Added block 0000e7e33f3c2252adf236f776a80eaacd32f35e08b933a9a894bc722f46253e
Received block command
Recevied a new block!
Added block 00000cbe359cc0de4aadd87351766ca20bc0ef357b2ffa6b8c146e0de17e3621
Received block command
Recevied a new block!
Added block 0000755097a897556bb23ba856e1ff605206979b4c8d6bd1446615592866c70c
Received block command
Recevied a new block!
Added block 00003014666abc9c04a9ac8a8668d1fca221fadd285ae67d12326ba315e6c472
```
