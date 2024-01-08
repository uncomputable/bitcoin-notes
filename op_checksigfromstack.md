# OP_CHECKSIGFROMSTACK

## Usage

```
<sig> <msg> <pk> OP_CHECKSIGFROMSTACK
```

## Execution

- require at least 3 stack elements
    - topmost: `pk` (bytes)
    - second topmost: `msg` (bytes)
    - third topmost: `sig` (bytes)
- if `pk` is an invalid public key
    - fail
- if `msg` is not 32 bytes
    - fail
- if `sig` is an ill-formatted signature
    - fail
- push bool::from(`sig` matches `pk` and `msg`)

## Explanation

Returns whether a given signature matches a given public key and message.

# OP_CHECKSIGFROMSTACKVERIFY

## Usage

```
<sig> <msg> <pk> OP_CHECKSIGFROMSTACKVERIFY
```

## Execution

- require at least 3 stack elements
    - topmost: `pk` (bytes)
    - second topmost: `msg` (bytes)
    - third topmost: `sig` (bytes)
- if `pk` is an invalid public key
    - fail
- if `msg` is not 32 bytes
    - fail
- if `sig` is an ill-formatted signature
    - fail
- if `sig` does not match `pk` and `msg`
    - fail

## Explanation

Fails if a given signature does not match a given public key and message.
