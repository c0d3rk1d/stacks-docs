# Stacks Node Configuration

{% hint style="info" %}
Note that these config fields are for a Stacks follower node. If you are running a signer alongside your Stacks node, you'll want to use the sample file found on the [Signer Configuration](sample-configuration-files.md) page as it contains additional parameters needed for your signer and Stacks node to function properly.
{% endhint %}

### Usage

```bash
stacks-node sub-command [--subcommand-option <value>]
```

#### Subcommands

* `mocknet`: start a mocknet instance using defaults
* `testnet`: start a testnet instance using defaults (chainstate is not persistent)
* `mainnet`: start a mainnet instance using defaults (chainstate is not persistent)
* `start`: combined with `--config`, starts an instance with a specified configuration file
* `version`: displays binary version
* `help`: displays the help message

### Configuration File Options

The Stacks Blockchain configuration file has multiple sections under which an option may be placed.

* node
* events\_observer
* connection\_options
* burnchain
* ustx\_balance
* miner

For reference, several configuration file examples are [available here](https://github.com/stacks-network/stacks-core/tree/master/sample/conf).

#### node

Contains various configuration options for the stacks-node binary.

| Name                         | Required | Description                                                                                                |
| ---------------------------- | -------- | ---------------------------------------------------------------------------------------------------------- |
| rpc\_bind                    | ✓        | IPv4 address and port to open for RPC connections                                                          |
| p2p\_bind                    | ✓        | IPv4 address and port to open for P2P connections                                                          |
| working\_dir                 |          | Absolute path to the directory where chainstate data will be stored                                        |
| data\_url                    |          | IPv4 address and port for incoming RPC connections                                                         |
| p2p\_address                 |          | IPv4 address and port for incoming P2P connections                                                         |
| bootstrap\_node              |          | Public key, IPv4 address, and port to bootstrap the chainstate                                             |
| wait\_time\_for\_microblocks |          | The amount of time in ms to wait before trying to mine a block after catching up to the anchored chain tip |
| seed                         |          | The private key to use for mining. Only needed if `miner` is set to `true`                                 |
| local\_peer\_seed            |          | The private key to use for signing P2P messages in the networking stack                                    |
| miner                        |          | Determines whether the node is running a follower (`false`) or a miner (`true`). Defaults to `false`       |
| mock\_mining                 |          | Simulates running a miner (typically used for debugging)                                                   |
| mock\_mining\_output\_dir    |          | Folder for mock mining data                                                                                |
| mine\_microblocks            |          | Determines whether the node will mine microblocks. Will only take effect if `miner` is set to `true`       |
| prometheus\_bind             |          | Address and port for Prometheus metrics collection.                                                        |
| deny\_nodes                  |          | List of ip addresses of nodes that should be ignored                                                       |
| stacker                      |          | Determines whether the node is running a stacker (`true`) that issues events for signer binary             |

#### events\_observer

{% hint style="info" %}
This section is _optional_ and not required

However, if this section is added, **all** fields are required.
{% endhint %}

Contains options for sending events emitted to the [stacks-blockchain-api](https://github.com/hirosystems/stacks-blockchain-api) service.

| Name         | Required | Description                                                                                                                                                       |
| ------------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| endpoint     | ✓        | Address and port to a [stacks-blockchain-api](https://github.com/hirosystems/stacks-blockchain-api) service                                                       |
| events\_keys | ✓        | Event keys for which to watch. The emitted node events can be restricted by account, function name and event type. Asterix ("\*") can be used to emit all events. |

#### connection\_options

{% hint style="info" %}
This section is _optional_ and not required.
{% endhint %}

Specifies configuration options for others connecting to the stacks node.

| Name                                  | Required | Description                                                                                                                                                 |
| ------------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| public\_ip\_address                   |          | Public IPv4 to advertise to other nodes                                                                                                                     |
| download\_interval                    |          | Time (in seconds) between attempts to download blocks                                                                                                       |
| walk\_interval                        |          | Time (in seconds) between attempts to walk the list of neighbors                                                                                            |
| private\_neighbors                    |          | If false, this node won't announce or accept neighbors that are behind private networks. Defaults to true.                                                  |
| read\_only\_call\_limit\_read\_length |          | Total number of bytes allowed to be read by an individual read-only function call                                                                           |
| read\_only\_call\_limit\_read\_count  |          | Total number of independent read operations permitted for an individual read-only function call                                                             |
| read\_only\_call\_limit\_runtime      |          | [Runtime cost](https://github.com/stacksgov/sips/blob/main/sips/sip-006/sip-006-runtime-cost-assessment.md) limit for an individual read-only function call |

#### burnchain

This section contains configuration options pertaining to the blockchain the stacks-node binds to on the backend for proof-of-transfer (BTC).

| Name       | Required | Description                                                                                                           |
| ---------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| chain      | ✓        | The blockchain stacks-node binds to on the backend for proof-of-transfer. Only value supported: `bitcoin`             |
| mode       | ✓        | The profile or test phase of which to run stacks-node. Valid values are \[ `mocknet`, `testnet`, `xenon`, `mainnet` ] |
| peer\_host |          | FQDN of the host running the backend Bitcoin blockchain                                                               |
| rpc\_port  |          | RPC port of `peer_host`                                                                                               |
| peer\_port |          | P2P port of `peer_host`                                                                                               |

**Mining**

| Name                             | Required | Description                                                                                        |
| -------------------------------- | -------- | -------------------------------------------------------------------------------------------------- |
| burn\_fee\_cap                   | ✓        | Maximum amount (in sats) of "burn commitment" to broadcast for the next block's leader election    |
| satoshis\_per\_byte              | ✓        | [Amount (in sats) per byte](https://bitcoinfees.net/) - Used to calculate the transaction fees     |
| commit\_anchor\_block\_within    |          | Sets the time period (in milliseconds) for commitments. Only used when `mode` is set to `mocknet`. |
| tenure\_extend\_cost\_threshold  |          | Percentage of block budget that must be used before attempting a time-based tenure extend          |
| block\_rejection\_timeout\_steps |          | Define the timeout to apply while waiting for signers responses, based on the amount of rejections |

#### ustx\_balance

{% hint style="info" %}
This section is only required for the `testnet` and `mocknet` networks.

However, if this section is added, **all** fields are required.
{% endhint %}

This section contains configuration options allocating microSTX per address in the genesis block

This section can repeat multiple times, but each section can only define a single address.

| Name    | Required | Description                                                           |
| ------- | -------- | --------------------------------------------------------------------- |
| address | ✓        | Address which maintains a microSTX balance                            |
| amount  | ✓        | The balance of microSTX given to the address at the start of the node |

### Example Mainnet Follower Configuration

```toml
[node]
working_dir = "/stacks-blockchain"
rpc_bind = "0.0.0.0:30443"
p2p_bind = "0.0.0.0:20444"
bootstrap_node = "02196f005965cebe6ddc3901b7b1cc1aa7a88f305bb8c5893456b8f9a605923893@seed.mainnet.hiro.so:20444,02539449ad94e6e6392d8c1deb2b4e61f80ae2a18964349bc14336d8b903c46a8c@cet.stacksnodes.org:20444,02ececc8ce79b8adf813f13a0255f8ae58d4357309ba0cedd523d9f1a306fcfb79@sgt.stacksnodes.org:20444,0303144ba518fe7a0fb56a8a7d488f950307a4330f146e1e1458fc63fb33defe96@est.stacksnodes.org:20444"

[burnchain]
chain = "bitcoin"
mode = "mainnet"
peer_host = "localhost"
peer_port = 8333

[[events_observer]]
endpoint = "localhost:3700"
events_keys = ["*"]
```

### Example Testnet Follower Configuration

```toml
[node]

rpc_bind = "0.0.0.0:20443"
p2p_bind = "0.0.0.0:20444"
bootstrap_node = "029266faff4c8e0ca4f934f34996a96af481df94a89b0c9bd515f3536a95682ddc@seed.testnet.hiro.so:30444"
prometheus_bind = "127.0.0.1:9153"
working_dir = "/stacks-blockchain"

[burnchain]
chain = "bitcoin"
mode = "krypton"
peer_host = "bitcoin.regtest.hiro.so"
peer_port = 18444
pox_prepare_length = 100
pox_reward_length = 900

[[ustx_balance]]
address = "ST2QKZ4FKHAH1NQKYKYAYZPY440FEPK7GZ1R5HBP2"
amount = 10000000000000000

[[ustx_balance]]
address = "ST319CF5WV77KYR1H3GT0GZ7B8Q4AQPY42ETP1VPF"
amount = 10000000000000000

[[ustx_balance]]
address = "ST221Z6TDTC5E0BYR2V624Q2ST6R0Q71T78WTAX6H"
amount = 10000000000000000

[[ustx_balance]]
address = "ST2TFVBMRPS5SSNP98DQKQ5JNB2B6NZM91C4K3P7B"
amount = 10000000000000000

[fee_estimation]
fee_estimator = "fuzzed_weighted_median_fee_rate"

[[burnchain.epochs]]
epoch_name = "1.0"
start_height = 0

[[burnchain.epochs]]
epoch_name = "2.0"
start_height = 0

[[burnchain.epochs]]
epoch_name = "2.05"
start_height = 1

[[burnchain.epochs]]
epoch_name = "2.1"
start_height = 2

[[burnchain.epochs]]
epoch_name = "2.2"
start_height = 3

[[burnchain.epochs]]
epoch_name = "2.3"
start_height = 4

[[burnchain.epochs]]
epoch_name = "2.4"
start_height = 5

[[burnchain.epochs]]
epoch_name = "2.5"
start_height = 6

[[burnchain.epochs]]
epoch_name = "3.0"
start_height = 1_900

[[burnchain.epochs]]
epoch_name = "3.1"
start_height = 2_000

[[burnchain.epochs]]
epoch_name = "3.2"
start_height = 71_525
```
