# Crash Fault Tolerant (CFT)

This is a simplified implemenation of Crash Fault Tolerant (CFT) similar to Raft consensus algorithm. It does not include Byzantine fault handling.

## Consensus Process

1. PrePrepare Phase (Leader Proposal).

* The leader node (n1) proposes a value (e.g., "Block_123").
* The leader send proposal to all nodes

2. Prepare Phase (Voting on Proposal)

* Nodes receive the Prepare message and store it.
* If at least 2 nodes receive the same value (simplified quorum condition), they send Commit messages to all peers.

3. Commit Phase (Final Agreement)

* Nodes receive Commit messages and store them.
* If at least 2 nodes receive matching Commit messages, the block is considered committed.

## Algorithm Design

Node 1 (Leader) -> PrePrepare: Block_123
Node 2 -> Prepare: Block_123 from Node 1
Node 3 -> Prepare: Block_123 from Node 1
Node 4 -> Prepare: Block_123 from Node 1
Node 2 -> Commit: Block_123 from Node 3
Node 3 -> Commit: Block_123 from Node 2
Node 4 -> Commit: Block_123 from Node 3
Node 2 -> COMMITTED: Block_123
Node 3 -> COMMITTED: Block_123
Node 4 -> COMMITTED: Block_123

### Why this is Crash Fault Tolerant (CFT) and not full pBFT?

This example shares similarities with Raft consensus rather than full pBFT because:

* Leader-driven: The process is controlled by a single leader, similar to Raft.
* No Byzantine Fault Handling: All nodes behave honestly; no malicious nodes send conflicting messages.
* No Digital Signatures: Real pBFT requires cryptographic verification of messages.
* Simplified Quorum: Instead of `2f + 1`, this example only needs a simple majority.

### Next Steps to Implement Full pBFT

To make this a true Byzantine Fault Tolerant system, we can:

* Introduce Byzantine nodes that behave maliciously.
* Require a 2f + 1 quorum before committing a value.
* Add cryptographic signatures to ensure message authenticity.
* Implement view changes to handle leader failures.

This implementation serves as a foundation for understanding consensus mechanisms before moving to full pBFT.

## Implementation Architecture

1. Message Structure

The Message struct represents a consensus message exchanged between nodes. It contains:

* Phase: The current phase (PrePrepare, Prepare, Commit).
* Sender: The ID of the node sending the message.
* Value: The transaction/block proposed for consensus.

2. Node Structure

Each Node has:

* id: A unique identifier.
* value: The value being proposed or agreed upon.
* peers: Other nodes participating in the consensus.
* prepare: Stores received Prepare messages.
* commit: Stores received Commit messages.

A mutex (sync.Mutex) to handle concurrent access to shared data.

