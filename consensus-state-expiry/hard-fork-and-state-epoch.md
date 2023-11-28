# Hard Fork & State Epoch

The State Expiry version introduces new features such as shadow tree, state epoch, reviveStateTx, state witness, and state revive, which need to be upgraded through hard fork.

  

At the same time, if all the states are no longer accessed, they will expire after 2 epochs. The epoch is like a ticker to indicate the expiry time of the state.

![](../assets/consensus-state-expiry/ss-hardfork.png)

The epoch3 and later epoch switching depends on the fixed EpochPeriod, which is 7008000 by default. According to the block production speed of 3s, each epoch lasts about 2/3 years.