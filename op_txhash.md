# TxFieldSector

## First byte

| Bit | Name                        |
|-----|-----------------------------|
| 0   | version                     |
| 1   | locktime                    |
| 2   | current input index         |
| 3   | current input control block |
| 4   | current input codesep pos   |
| 5   | inputs?                     |
| 6   | outputs?                    |
| 7   | control bit                 |

If the control bit is set, then the TxFieldSector itself is included in the hash.

## Second byte

Expected if `inputs?` or `outputs?` is set.

| Bit | Name                      |
|-----|---------------------------|
| 0   | all spent outpoints       |
| 1   | all input sequences       |
| 2   | all input scriptSig's     |
| 3   | all spent scriptPubKey's  |
| 4   | all spent amounts         |
| 5   | all input annexes         |
| 6   | all output scriptPubKey's |
| 7   | all output amounts        |

## Input bytes

Expected if `inputs?` is set.

### Leading mode

#### First byte

| Bit | Name                   |
|-----|------------------------|
| 0   | number of inputs       |
| 1   | specification mode = 0 |
| 2   | extra byte?            |
| 3   | number of inputs       |
| 4   |                        |
| 5   |                        |
| 6   |                        |
| 7   |                        |

Without an extra byte, up to 32 leading inputs can be selected.

#### Second byte

If extra byte is set.

| Bit | Name                         |
|-----|------------------------------|
| 0   | number of inputs (continued) |
| 1   |                              |
| 2   |                              |
| 3   |                              |
| 4   |                              |
| 5   |                              |
| 6   |                              |
| 7   |                              |

With an extra byte, up to 8192 leading inputs can be selected.

### Individual mode

#### First byte

| Bit | Name                        |
|-----|-----------------------------|
| 0   | number of inputs            |
| 1   | specification mode = 1      |
| 2   | number of individual inputs |
| 3   |                             |
| 4   |                             |
| 5   |                             |
| 6   |                             |
| 7   |                             |

Up to 64 individual inputs can be selected.

#### Individual input byte(s)

One byte expected for each individual input (as many as specified).

##### Absolute index

| Bit | Name                 |
|-----|----------------------|
| 0   | relative index = 0   |
| 1   | extra byte?          |
| 2   | absolute input index |
| 3   |                      |
| 4   |                      |
| 5   |                      |
| 6   |                      |
| 7   |                      |

Without an extra byte, inputs at index <64 can be selected.

Expect extra byte if specified.

| Bit | Name                             |
|-----|----------------------------------|
| 0   | absolute input index (continued) |
| 1   |                                  |
| 2   |                                  |
| 3   |                                  |
| 4   |                                  |
| 5   |                                  |
| 6   |                                  |
| 7   |                                  |

With an extra byte, inputs at index <16384 can be selected.

##### Relative index

| Bit | Name                 |
|-----|----------------------|
| 0   | relative index = 1   |
| 1   | negative sign?       |
| 2   | relative input index |
| 3   |                      |
| 4   |                      |
| 5   |                      |
| 6   |                      |
| 7   |                      |

Inputs less than 64 indices away from the current index can be selected.

## Output bytes

Expected if `outputs?` is set.

Same structure as the input bytes, except that outputs are specified.
