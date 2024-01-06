# BIP 118 Sighashes

## hash_type: u8

| Byte | Name                          | UTXOs signed | Inputs signed | Outputs signed              |
|------|-------------------------------|--------------|---------------|-----------------------------|
| 0x00 | DEFAULT                       | same         | all           | all                         |
| 0x01 | ALL                           | same         | all           | all                         |
| 0x02 | NONE                          | same         | all           | none                        |
| 0x03 | SINGLE                        | same         | all           | same index as current input |
| 0x41 | ALL \| ANYPREVOUT             | similar      | current       | all                         |
| 0x42 | NONE \| ANYPREVOUT            | similar      | current       | none                        |
| 0x42 | SINGLE \| ANYPREVOUT          | similar      | current       | same index as current input |
| 0x81 | ALL \| ANYONECANPAY           | same         | current       | all                         |
| 0x82 | NONE \| ANYONECANPAY          | same         | current       | none                        |
| 0x83 | SINGLE \| ANYONECANPAY        | same         | current       | same index as current input |
| 0xc1 | ALL \| ANYPREVOUTANYSCRIPT    | any          | current       | all                         |
| 0xc2 | NONE \| ANYPREVOUTANYSCRIPT   | any          | current       | none                        |
| 0xc3 | SINGLE \| ANYPREVOUTANYSCRIPT | any          | current       | same index as current input |

The "same" UTXOs have the same outpoint (txid + vout).

"Similar" UTXOs have the same amount and scriptPubKey.

"Any" UTXOs means that the signature permits any UTXO.

## SigMsg(hash_type: u8, ext_flag: u8) -> [u8]

```
m  = hash_type
m += nVersion
m += nLockTime
// Omit all inputs, like ANYONECANPAY
if hash_type is not (NONE or SINGLE):
    m += all outputs
m += (ext_flag * 2) + u8::from(annex is present)
if hash_type is ANYPREVOUT:  // Omit for ANYPREVOUTANYSCRIPT
    m += current spent amount
    m += current spent scriptPubKey
    // Omit spent outpoint, unlike ANYONECANPAY
m += current nSequence
// Omit current input index, like ANYONECANPAY
if annex is present:
    m += current annex
if hash_type is SINGLE:
    m += current output

return m
```

`ANYPREVOUT` is like `ANYONECANPAY` except that the spent outpoint is omitted.

`ANYPREVOUTANYSCRIPT` is like `ANYPREVOUT` except that the spent amount and spent scriptPubKey are omitted.

`ANYONECANPAY` has no functionality?!

## Ext(hash_type: u8) -> [u8]

```
e  = if hash_type is ANYPREVOUTANYSCRIPT { "" } else { tapleaf hash }
e += 0x01 (key version)
e += code separator version (SegWit detail)
```

`key_version` is set to `0x01`.

## Message(hash_type: u8) -> [u8; 32]

```
return hash_TapSighash(hash_type || SigMsg(hash_type, 0x01) || Ext(hash_type))
```

`ext_flag` is set to `0x01`.

`hash_type` is set to the final byte of a signature of 65 bytes and defaults to `0x00` for a signature of 64 bytes.
