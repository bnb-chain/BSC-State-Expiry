# Snapshot

In the state expiration scheme, snapshots still exist, and the Account Storage structure is mainly modified:

Account storage kv: key has no change, the value will contain an extra epochNum of the slot;

Therefore, when the Storage Slot is accessed, the Epoch information will be updated in time. If the epoch of the slot is behind by more than 1 epoch, it indicates that the status has expired and cannot be accessed, and it is waiting for prune.

  

Of course, when the Epoch is accessed and the status expires, it will not be written to the disk immediately, but will be stored in the diff layer, with a maximum depth of 128 diff layers.

```go
// SnapValue store snap with Epoch after BEP-216
type SnapValue struct {
	Epoch types.StateEpoch
	Val   common.Hash
}
```

![](../assets/consensus-state-expiry/jacksen_state_expiry-ss-snapshot-epoch.png)

  

Later support snap sync will more easy.
