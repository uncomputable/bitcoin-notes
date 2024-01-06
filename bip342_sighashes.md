# BIP 341 Sighashes

## hash_type: u8

| Byte | Name                    | Inputs signed | Outputs signed              |
|------|-------------------------|---------------|-----------------------------|
| 0x00 | DEFAULT                 | all           | all                         |
| 0x01 | ALL                     | all           | all                         |
| 0x02 | NONE                    | all           | none                        |
| 0x03 | SINGLE                  | all           | same index as current input |
| 0x81 | ALL \| ANYONECANPAY     | current       | all                         |
| 0x82 | NONE \| ANYONECANPAY    | current       | none                        |
| 0x83 | SINGLE \| ANYONECANPAY  | current       | same index as current input |

## SigMsg(hash_type: u8, ext_flag: u8) -> [u8]

```
m  = hash_type
m += nVersion
m += nLockTime
if hash_type is not ANYONECANPAY:
    m += all spent outpoints
    m += all spent amounts
    m += all spent scriptPubKey's
    m += all input nSequence's
if hash_type is not (NONE or SINGLE):
    m += all outputs
m += (ext_flag * 2) + u8::from(annex is present)
if hash_type is not ANYONECANPAY:
    m += current spent outpoint
    m += current spent amount
    m += current spent scriptPubKey
    m += current nSequence
if hash_type is not ANYONECANPAY:
    m += current input index
if annex is present:
    m += current annex
if hash_type is SINGLE:
    m += current output

return m
```

## Message(hash_type: u8) -> [u8; 32]

```
return hash_TapSighash(hash_type || SigMsg(hash_type, 0x00))
```

`ext_flag` is set to `0x00`.

`hash_type` is set to the final byte of a signature of 65 bytes and defaults to `0x00` for a signature of 64 bytes.

TapScript uses BIP 342 sighashes (see below).

# BIP 342 Sighashes

Extension of BIP 341 sighashes.

## `Ext: [u8]`

```
e  = tapleaf hash
e += 0x00 (key version)
e += code separator version (SegWit detail)
```

## Message(hash_type: u8) -> [u8; 32]

```
return hash_TapSighash(hash_type || SigMsg(hash_type, 0x01) || Ext)
```

`ext_flag` is set to `0x01`.

`hash_type` is set to the final byte of a signature of 65 bytes and defaults to `0x00` for a signature of 64 bytes.
