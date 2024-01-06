# OP_DETERMINISTICRANDOM

## Usage

```
<seed> <bnMin> <bnMax> OP_DETERMINISTICRANDOM
```

## Execution

- require at least 3 stack elements
    - topmost: bnMax (number)
    - second topmost: bnMin (number)
    - third topmost: seed (bytes)
    - fail otherwise
- if bnMax < bnMin
    - fail
- if bnMin == bnMax
    - pop three stack elements
    - push bnMin
    - return
- nMax := bnMax - bnMin
- nRange := largest multiple of nMax less equal u64::MAX
- initialize hash engine with seed
- i := 0
- j := 3
- while nRange < nRand
    - if 3 <= j
        - write i into hash engine (updating current midstate)
        - j := 0
    - nRand := j-th quarter of current hash midstate
        - each quarter is 64 bits long
    - j += 1
- bnRand := nRand % nMax + bnMin
- assert bnMin <= bnRand <= bnMax
- pop three stack elements
- push bnRand
- return

## Explanation

The goal is to produce a uniformly random number in the internal `[bnMin, bnMax)`.

The internal is shifted left to `[0, nMax)` where `nMax + bnMin = bnMax`.

This interval is extended to `[0, nMax * n)` for some `n > 0` such that `nMax * n` is as close to `u64::MAX` as possible.

The hash engine is initialized with the given seed and the counter = 0.

Each quarter of the current midstate is a pseudorandom 64-bit number.

The first quarter that is less than `nMax * n` is taken as `nRand`.

If no quarter satisfies this condition, then the counter is incremented by one and the midstate is updated.

A suitable `nRand` will always be found after finitely many iterations.

`bnRand` is taken as `(nRand mod nMax) + bnMin`.

`nRand` is uniformly random in `[0, nMax * n)`, so `nRand mod nMax` is uniformly random in `[0, nMax)`. 

Following up, `(nRand mod nMax) + bnMin` is uniformly random in `[bnMin, nMax + bnMin) = [bnMin, bnMax)`.
