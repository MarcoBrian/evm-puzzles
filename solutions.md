# EVM Puzzles Solution

Github Link for the EVM Puzzles: https://github.com/MarcoBrian/evm-puzzles/tree/master
## Puzzle 1

**Solution** : The goal is to skip the revert opcodes and end the program successfully by reaching `STOP`

Answer: Call value = 8 

**Explanation:** 
We can complete the puzzle by entering value 8. 

1. The first opcode is `CALLVALUE` this will push the value 8 to the EVM stack. 
2. The next opcode is `JUMP` and the value 8 is popped out from the stack, which then means we will jump to slot 8. 
3. Afterwards we are able to stop the program. 


## Puzzle 2 

**Solution** : To solve this puzzle, we need to jump to the instruction at byte `06`, which contains a valid `JUMPDEST`.

**Answer:** 4 

**Explanation:** 
The logic of the code is:
1. `CALLVALUE` - Pushes the amount of wei sent (`x`) onto the stack
2. `CODESIZE` - Pushes the total length of the contract bytecode (which is 10 bytes here)
3. `SUB` - Subtracts `x` from 10 → gives `10 - x`
4. `JUMP` - Jumps to the byte offset `10 - x`
5. If the jump destination is invalid → execution fails 
6. If we land on `JUMPDEST` at offset `06` → execution continues and hits `STOP` (success)

So we send the value `4`, making the jump land at byte `06` where `JUMPDEST` is located and the program successfully halts with `STOP`.

## Puzzle 3

**Solution** : To solve this puzzle, we can send any 4 bytes of data. 
**Answer:** `0x11223344`

**Explanation:** 
1. CALLDATASIZE -> this opcode will take the byte size of the call data 
2. The JUMPDEST opcode is at location 4 , so to complete the puzzle we can input any 4 bytes of data. 



## Puzzle 4

**Solution:** The goal is to find a value that when this value performs XOR with the code size , equals to 0C in hex (12 in decimal) will result in the value 0A (which is the JUMPDEST location)

**Answer:** `6`


**Explanation:** 

| Value | Binary | Decimal | Hex    |
| ----- | ------ | ------- | ------ |
| A     | `1100` | `12`    | `0x0C` |
| B     | `0110` | `6`     | `0x06` |
| A ⊕ B | `1010` | `10`    | `0x0A` |

## Puzzle 5 

**Answer:** 16
**Explanation:** 
16 * 16 = 256 = 0x0100 in hex. 
There is an `EQ` operation that checks to make sure the value is equal to x0100 
When the value is true, then it will `JUMPI` to the correct destination 

## Puzzle 6

**Answer:**`0x000000000000000000000000000000000000000000000000000000000000000a` 
**Explanation:** 
The goal is to JUMP to location 0A, since the information to jump is retrieved from calldata , we need to load the call data value. 

### Puzzle 7 

**Answer:** `0x60016000F3`
**Explanation:** 

The goal here is to be able to provide a contract code (calldata) such that the created run time byte code has a size of 1 byte. 

We can track the EVM stack at every operation:
1. stack: {calldatasize}
2. stack: {calldatasize, 0}
3. stack: {calldatasize, 0, 0 }
4. `CALLDATACOPY(0,0,calldatasize)`  => store calldata bytecode in `memory[0]`, memory location 0. 
5.  stack: {calldatasize}
6. stack: {calldatasize, 0}
7. stack: {calldatasize, 0, 0 }
8. `CREATE(0,memory[0],calldatasize)` => Creates a new contract and returns address to the stack
9. `EXTCODESIZE(ADDRESS)` => returns the byte size of the account code
10. When the contract size is 1, it will jump to the correct destination 

So the goal here is we need to create a calldata with bytecode that will return 1 byte size of code. 

The call data here is a contract creation code, this will return an empty code `0x00` of size 1 byte. 

```
PUSH1 0x01 (60 01)
PUSH1 0x00 (60 00)
RETURN (F3)
```


When translated the above opcodes to hex this will become `0x60016000F3` 



### Puzzle 8 

**Answer:** `0x60fd6000526001601ff3` 

**Explanation:** 

The goal is that we need to make the specific CALL opcode fail so that it will be able to `JUMP` and stop the program

Specifically the program will need to create a contract first from the call data value. The following step is that we need to make sure the program fails which means we need it to return the `REVERT` (FD) opcode

```
PUSH1 0xfd    ; 1 byte value to return
PUSH1 0x00    ; offset
MSTORE        ; store at memory[0]
PUSH1 0x01    ; size
PUSH1 0x1f    ; offset = 31 (so last byte has 0xfd)
RETURN
```


This above opcodes translates to `0x60fd6000526001601ff3`

### Puzzle 9

Answer:
- calldata: `0x00112233`
- call value => `2`



### Puzzle 10

**Answer:**
- Call Value: 15
- Call data: `0x001122`
