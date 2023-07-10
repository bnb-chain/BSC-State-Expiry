# Partial Revive

According to the design of BEP-206, the shadow node will record the metadata of when the MPT node is accessed. This feature can be used for partial revival and saves gas in batch revival scenarios.

When a large number of states in Storage Trie has expired, the whole sub-tries will be pruned.

[![](https://github.com/0xbundler/BEPs/raw/state_expiry_new_tx/assets/BEP-215/partial_revival_example1.png)](https://github.com/0xbundler/BEPs/blob/state_expiry_new_tx/assets/BEP-215/partial_revival_example1.png)

The partial revive allows the user to revive the state in batch mode and only revive the left part. At this time, the trie nodes could be revived layer by layer to the storage MPT, thereby reducing the gas consumption of batch state revival.

The revived trie nodes rebuild the Shadow Nodes to indicate which child have been revived, or which are still expired.

[![](https://github.com/0xbundler/BEPs/raw/state_expiry_new_tx/assets/BEP-215/partial_revival_witness.png)](https://github.com/0xbundler/BEPs/blob/state_expiry_new_tx/assets/BEP-215/partial_revival_witness.png)

  

When you first revive C, you need combine Witness\[0\], Witness\[1\] and Witness\[5\] in ReviveStateTx. But if you want to revive D later, you only need provide Witness\[6\] is enough...


