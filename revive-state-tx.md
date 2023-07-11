# ReviveStateTX

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