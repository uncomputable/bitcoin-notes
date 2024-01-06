# OP_CHECKTEMPLATEVERIFY

## Usage

```
<hash> OP_CTV
```

## Execution

- require at least 1 stack element
    - topmost: hash (bytes)
    - fail otherwise
- if hash is not 32 bytes long
    - return
- if hash != DefaultCheckTemplateVerifyHash
    - fail

## DefaultCheckTemplateVerifyHash: [u8; 32]

```
d  = nVersion
d += nLockTime
d += all input scriptSig's
d += number of inputs
d += all input nSequences's
d += number of outputs
d += all outputs
d += current index

return sha256(d)
```
