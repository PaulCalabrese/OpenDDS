## H2 Introduction
This example adds a new EOS node to an already existing testnet. It first starts a single process containing 20 producers. We can then observe this network running and producing new blocks. Later a new node is launched with a 21st producer which joins the network, synchronizes, and produces blocks in its turn.

## H2 Instructions

 1. The `data_dir1` and `data_dir2` directories should be placed in the `eos/build` directory of a built eos installation.
 2. The `config.ini` file in `data_dir1` defines a process that runs 20 producers:
```
    genesis-json = ./genesis.json
    http-server-address = 127.0.0.1:8888
    p2p-listen-endpoint = 0.0.0.0:9876
    p2p-server-address = localhost:9876
    private-key = ["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
    producer-name = inita
    producer-name = initb
    producer-name = initc
    producer-name = initd
    producer-name = inite
    producer-name = initf
    producer-name = initg
    producer-name = inith
    producer-name = initi
    producer-name = initj
    producer-name = initk
    producer-name = initl
    producer-name = initm
    producer-name = initn
    producer-name = inito
    producer-name = initp
    producer-name = initq
    producer-name = initr
    producer-name = inits
    producer-name = initu
    plugin = eosio::producer_plugin
    plugin = eosio::chain_api_plugin
```
3. It can be started with the following command:
```
    programs/eosiod/eosiod --enable-stale-production true --data-dir data_dir1 >& data_dir1/std.txt &
```
4. You can verify that blocks are being produced using the following command:
```
    programs/eosioc/eosioc -p8888 get info
```
Repeat this command until you see the last_irreversible_block_num begin increasing.
5. A process containing the 21st producer is defined in the `data_dir2/config.ini` file:
```
    genesis-json = ./genesis.json
    http-server-address = 127.0.0.1:8889
    p2p-listen-endpoint = 0.0.0.0:9877
    p2p-server-address = localhost:9877
    p2p-peer-address = localhost:9876
    private-key = ["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
    producer-name = initt
    plugin = eosio::producer_plugin
    plugin = eosio::chain_api_plugin
```
6. The only difference in this configuration are different port number for the p2p and http connections, an active connection to the other process, and the single producer (in place of the twenty in the other file).
Start it with this command:
```
    programs/eosiod/eosiod --data-dir data_dir2 >& data_dir2/std.txt &
```
7. You can verify that the two processes are in sync with the following commands:
```
    ./programs/eosioc/eosioc -p 8888 get info ; ./programs/eosioc/eosioc -p 8889 get info
```You should see the head_block_num and last_irreversible_block_num increasing in tandem.
8. You can also verify that the second process' producer is generating blocks by looking in the data_dir2/std.txt log file. You should periodically (every minute or so) see a group of eight blocks produced by the initt producer. These lines of the log file should look something like this:
```
    initt generated block 0c71b76a... #240 @ 2018-01-23T20:33:19.000 with 0 trxs
    initt generated block 8bc44f51... #241 @ 2018-01-23T20:33:19.500 with 0 trxs
    initt generated block 7ef8f2c0... #242 @ 2018-01-23T20:33:20.000 with 0 trxs
    initt generated block 1944b0de... #243 @ 2018-01-23T20:33:20.500 with 0 trxs
    initt generated block 0154b8df... #244 @ 2018-01-23T20:33:21.000 with 0 trxs
    initt generated block 091292d3... #245 @ 2018-01-23T20:33:21.500 with 0 trxs
```

Note: If you wish to run this example again, kill the running eosiod processes and delete the blockchain history directories:
```
    rm -rf data_dir1/blocks data_dir1/shared_mem 
    rm -rf data_dir2/blocks data_dir2/shared_mem 
```
