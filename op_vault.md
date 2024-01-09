# OP_VAULT

## Usage

```
<revault_amount> <revault_index> <trigger_index> <data_1> ... <data_n> <n> <leaf_script> OP_VAULT
```

## Execution

- require 2 stack items
    - `leaf_script` (bytes)
    - `n` (number)
    - fail otherwise
- require `n` + 3 more stack items
    - `data_1` (bytes)
    - ...
    - `data_n` (bytes)
    - `trigger_index` (number)
    - `revault_index` (number)
    - `revault_amount` (number)
    - fail otherwise
- `leaf_update_script` := `<data_1> ... <data_n> leaf_script`
    - append `data_*` in front of `leaf_script`
- if `trigger_index` < 0
    - fail
- if `revault_index` < -1
    - fail
    - `revault_index = -1` signifies absence of an index
- if `revault_amount` < 0
    - fail
- CheckVaultTrigger(`leaf_update_script`, `trigger_index`, `revault_index`, `revault_amount`)
- pop `n` + 5 stack elements
- push TRUE

## Explanation

`OP_VAULT` parses a list of arguments:

1. `leaf_script`
2. `data_1`, ..., `data_n`
3. `trigger_index`
4. `revault_index`
5. `revault_amount`

`revault_index` is optional, which is encoded by `revault_index = -1` (yay).

The leaf update script is constructed by combining the `data_*` items with `leaf_script`.

If `CheckVaultTrigger` succeeds, then the arguments are popped off the stack and `TRUE` is pushed onto the stack.

## CheckVaultTrigger(`leaf_update_script`: [u8], `trigger_index`: u32, `revault_index`: u32, `revault_amount`: u64)

- `trigger_out` := output at `trigger_index`
    - fail if output doesn't exist
- if scriptPubkey of `trigger_out` is not Taproot
    - fail
- `pk` := output key of `trigger_out`
    - output key is part of scriptPubKey
- `merkle_root` := combine current control block and `leaf_update_script`
    - tappath plus hash of `leaf_update_script`
- `expected_pk` := tweak current internal key with `merkle_root`
- if `pk` != `expected_pk`
    - fail
- `total_amount` := amount of `trigger_out`
- `expected_amount` := current input amount (spent UTXO)
- if 0 <= `revault_index`
    - `total_amount` += CheckRevault(`revault_index`, `revault_amount`)
- else if `revault_amount` != 0
    - fail (malleability)
- if `total_amount` < `expected_amount`
    - fail

## Explanation

Enforce that the output at the trigger index has a particular taptree:

Take the taptree of the spent vault UTXO and replace the current leaf with the leaf update script.

If there is a revault index, then run CheckRevault. Otherwise, `revault_index = 0` is mandatory to prevent malleability.

Enforce that all satoshis of the spent vault UTXO go into the trigger and revault output.

## Taproot refresher

The Taproot control block contains:

1. Leaf version
1. Parity of output key
1. Internal key
1. Tappath

The tappath is an inclusion proof of the leaf script for the taptree. The tappath does not include the hash of the leaf script itself.

## CheckRevault(`revault_index`: u32, `revault_amount`: u64) -> u64

- `revault_out` := output at `revault_index`
    - fail if output doesn't exist
- if scriptPubKey of `revault_out` != current input scriptPubKey (spent UTXO)
    - fail
- if `revault_amount` <= 0 or (amount of `revault_out`) < `revault_amount`
    - fail
- return amount of `revault_out`

## Explanation

Enforce that the output at the revault index has the same taptree as the spent vault UTXO.

Enforce that the revault output has at least the given revault amount.

Return the amount of the revault output.

## Overview

`OP_VAULT` enforces that the output at the trigger index has the trigger script.

Index and trigger script are arguments to `OP_VAULT`.

Optionally, `OP_VAULT` enforces that there is an output at the revault index that (recursively) repeats the covenant. This output must be at least as big as the revault amount.

Index and amount are again arguments.

`OP_VAULT` enforces that all satoshis of the spent vault UTXO go into the trigger and revault output.

## References

[Draft BIP (outdated)](https://github.com/jamesob/bips/blob/jamesob-23-03-opvault-rework/bip-vaults.mediawiki)

[Code](https://github.com/jamesob/bitcoin/blob/2023-02-opvault-inq/src/script/interpreter.cpp)
