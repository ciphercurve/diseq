![Diseq](https://github.com/jonas089/DISEQ/blob/master/resources/DISEQ/DISEQ1.jpg)

The logos were created by [Imran Piash](https://www.fiverr.com/imranpiash?source=inbox). Rights have been granted to Jonas Pauli (the developer of this project).

# DISEQ Proof of Concept: Distributed Message Sequencing
Diseq is a distributed sequencer built by [Jonas Pauli](https://www.linkedin.com/in/jonas-pauli/), a blockchain research engineer from Switzerland.
The project is associated with [Ciphercurve GmbH](https://ciphercurve.com), a software company based in Zug-Baar (ch).

Contact: *contact@ciphercurve.com*

Diseq acts as a distributed alternative to centralized (or decentralized) sequencing. Based on a novel zero knowledge consensus with deterministic validator selection, Diseq can operate with n > 50% percent of a fixed validator set being active and honest. Messages are added to a mempool and stored in the block once consensus has concluded and sufficiently many signatures from active nodes were collected. Nodes synchronize blocks to keep an immutable record of the message sequence.

Read the full [Litepaper](https://github.com/jonas089/zk-vrf-consensus/tree/master/whitepaper).

If you are an expert in consensus mechanisms, consider reading [some context about BFT](https://github.com/jonas089/zk-vrf-consensus/blob/master/whitepaper/byzantine-fault.md).
# Recommended: Run a local network of 4 Nodes with Docker
I began taking this passion project quite seriously, so I added an SQLite DB to store Blocks and Messages.
Messages are still read as a single chunk so the txpool for each Block must fit in memory, I do intend to change this.

To run the docker image with 2 nodes that will each have a db e.g. node-1.sqlite, node-2.sqlite where the temporary txpool and all
finalized Blocks are stored, run:

```bash
docker compose up
```

Port forwarding should make the nodes available a `8080` and `8081`. I plan to simulate larger networks in the future but for now it is designed
to spawn 2 instances that synchronize blocks and commit to proposals / contribute to consensus. The default consensus threshold is `1` - see `config` directory.

# API Routes

## Internal
```rust
        .route("/schedule", post(schedule))
        .route("/commit", post(commit))
        .route("/propose", post(propose))
        .route("/merkle_proof", post(merkle_proof))
```
## External
```rust
        .route("/get/pool", get(get_pool))
        .route("/get/commitments", get(get_commitments))
        .route("/get/block/height", get(get_block))
        .route("/get/state_root_hash", get(state_root_hash))
```

To view a Block when running the example setup, request `127.0.0.1:8080/get/block/<id>`, or `127.0.0.1:8081/get/block/<id>`.

# Merkle Proofs
Whenever a Block is stored, all messages in that block are inserted into the custom [Merkle Patricia Trie](https://github.com/jonas089/jonas089-trie).
For every individual message in the trie a merkle proof can be obtained. See an example for this [here](https://github.com/jonas089/distributed-sequencer/blob/master/tests/api.rs).

# Vision and Milestones
In order for this project to evolve into a practical alternative to other sequencing solutions, shared sequencing must be implemented. Currently nodes take turns in submitting sequenced transactions from their pool. This is a good start, but not distributed to the degree that we want to achieve. Nodes should be modified to synchronize their transaction pool before the block proposal stage, and a commitment over a deterministically established, sequence should be included in the block. 

Once the implementation of said feature has been completed and some of the open issues have been addressed, the sequencer will be ready for use in a decentralized context. This could imply introducing a tokenomic model in order for new validators to join the network and perhaps to form an economic incentive for those operating these nodes, or setting up shared instances with partners to run distributed, not decentralized, sequencing infrastructure.

Currently we do not plan to add a token to the Diseq codebase, but it might become feasible to build a tokenomic model and decentralized sequencer on top of this distributed basis in the future.

*Copyright [Ciphercurve Gmbh](https://ciphercurve.com), 2025*
