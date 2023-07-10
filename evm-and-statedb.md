# EVM & StateDB

EVM is mainly related to state operations, mainly including two opcodes, sload and sstore, and interpreter, stateDB and other modules.

The transactions in the block will be executed sequentially. The specific execution process is shown in the figure below:




#### ApplyMessage

![](https://t25652588.p.clickup-attachments.com/t25652588/bf07e0a6-6e30-456a-9209-b630174acd93/jacksen_state_expiry-ss-evm-applymsg.png)

When executing a specific transaction, if there is a WitnessList, it will revive the state first. When the execution fails, the revert operation will restore all the states to the version before execution.

  

#### Finalize

![](https://t25652588.p.clickup-attachments.com/t25652588/3a14bd39-482e-40b7-b3c6-0ef4441ae85b/jacksen_state_expiry-ss-evm-finalise.png)

When the transaction is successfully executed, all dirty states will be changed to pending state, and subsequent transactions can share all previous changes, but at this time it will not affect the underlying DB.

  

#### Commit

![](https://t25652588.p.clickup-attachments.com/t25652588/2da83bd2-2249-4308-9840-0f46fab6ed50/jacksen_state_expiry-ss-evm-commit.png)

When all transactions in the block are executed and the block meets the submission requirements, such as sync verification or mining seal, all pending states will be submitted to the underlying DB.

# ReviveStateTx

The [BEP215](https://github.com/bnb-chain/BEPs/pull/215) introduce a new transaction, `ReviveStateTx`.

```plain
type SignedReviveStateTx struct {
	message: ReviveStateTx
	signature: ECDSASignature
}
type ReviveStateTx struct {
	Nonce    	uint64          // nonce of sender account 
	GasPrice 	*big.Int        // wei per gas
	Gas      	uint64          // gas limit 
	To       	*common.Address // nil means contract creation 
	Value    	*big.Int        // wei amount 
	Data     	[]byte          // contract invocation input data 
	WitnessList	[]ReviveWitness  
}
```

It based on `legacyTx` structure, add `WitnessList` field, support revive expired state first, and execute the tx as `legacyTx` .

  

The `ReviveStateTx` encode/decode as `TypedTransaction` will support in [BEP229](https://github.com/bnb-chain/BEPs/pull/229), reverse 127 as `ReviveStateTxType`.

  

There also introduce a new tx signer, `BEP215Signer` , it is compatible with `EIP155Signer` , that contains `chainId`.

  

If Berlin & London enable on BSC, it may be compatible with their tx & signer too.

#### Gas

In new transaction type, it introduces an additional field of StateWitness, this part will define the extra gas cost, whoever revive that state will have to pay for it. It consists of three parts:

1. StateWitness size cost, and the Gas consumption per byte is defined as `WITNESS_BYTE_COST` ;
2. StateWitness verify cost, and the fixed Gas consumption of each branch is defined as `WITNESS_VERIFY_MPT_BASE_COST`, `WITNESS_VERIFY_MPT_PER_WORD_COST`;
3. Revive the state cost, same as a `sStore` opcode cost;

The intrinsic gas calculation rules are as follows:

```plain
func WitnessIntrinsicGas(ws []StateWitness) uint64 {
  totalGas := 0
  for i:=0; i<len(ws); i++ {
    // add witness size cost
    totalGas += len(ws[i].bytes()) * WITNESS_BYTE_COST
    totalGas += WITNESS_VERIFY_MPT_BASE_COST + len(ws[i].words()) * WITNESS_VERIFY_MPT_PER_WORD_COST
  }
  return totalGas
}
```

As you can see, the cost of state revive is higher than that of a simple store, depending on the size of the Witness.