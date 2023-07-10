# Prune

## MPT Prune

### Offline Prune

No modifications are needed for offline pruning. Since expired nodes will not be accessed, they will be pruned as usual.

  

### Inline Prune

A new condition is added to check if the node is expired, it will be dereferenced. However, the condition is adjusted based on BEP-206:

> These expired trie nodes can be pruned, but in order to keep the proof generation capability, it is better to only prune the shadow node that is >=2 epochs behind its parent.

  

**Some Challenges Faced**

1. Cannot differentiate between account trie node and storage trie node during inline prune
    *   All trie nodes have `epoch` field. Since the epoch's default value is 0, when checking for inline prune condition, account trie nodes will be pruned as well.
    *   **Solution**: Change `epoch` default value to `nil` so that we can differentiate between the account trie node and storage trie node.
    *   **Temporary Fix**: Add a condition to not prune nodes with epoch 0
2. `RootNode` cannot be found in `TrieDb` , so the entire storage trie cannot be pruned
    *   The initial design is that `RootNode` is stored in the shadow node database because it is a special type of node, which does not implement the `node` interface.
    *   However, the hash of `RootNode` is stored as the storage root in a `StateAccount`
    *   **Solution**: Make `RootNode` implement the `node` interface and store it in `TrieDb`

  

## ShadowTree Prune

For ArchiveNode, no data will be pruned, but for FullNode, the history&changeSet of shadowNodes will be pruned, and the expired PlainState will also be Pruned, which is temporarily done in offline prune.

  

It could implement an inline prune later.

  

### Snapshot Prune

The main way for snapshots to prune expired KV is through offline prune. When scanning the DB and snapshot KV is found, then check the kv if has expired, and prune it.

  

Of course, pruning expired nodes does not affect the normal logic of full nodes, so it can be run as a background task and can be optimized later.


