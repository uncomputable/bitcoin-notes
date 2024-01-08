# OP_CAT

## Usage

```
<siamese> <persian> OP_CAT
```

## Execution

- require at least 2 stack elements
    - topmost: `b` (bytes)
    - second topmost: `a` (bytes)
    - fail otherwise
- pop 2 stack elements
- push `a` ++ `b`

## Explanation

Concatenates two stack elements.

The topmost element becomes the **rear part** of the result.

The second topmost element becomes the **front part**.
