# Data Format

Much of this data formatting is inspired by the NASM compiler.

## Registers

Columns:

- Register Name
- Register Class
- Register Number

## Instructions

Columns:

- Instruction Mnemonic
  - Uppercase alpha-numeric identifier
  - May use "cc" to expand to conditional mnemonic
- Instruction Operands
  - Comma-separated list of operands
- OpCode Bytes
  - List of space-separated bytes contained in square brackets
  - Bytes are uppercase hex values
  - Operands are referenced through lowercase non-hex values
  - Conditionals are referenced through a lowercase 'c' character

### Instruction Operands

| Argument | Description                     |
| :------- | :------------------------------ |
| void     | No operands taken               |
| immN     | N-bit immediate data            |
| mem      | Memory offset register          |
| rmN      | N-bit register or memory        |
| _other_  | Register class or register name |

### OpCode Bytes

The opcode bytes are (generally) a list of hexadecimal bytes contained in square brackets.

Operands are included in the opcode bytes list through a list of shorthand references, depicted as a list of dashes or non-hexadecimal letters between the opening bracket and a colon. These same letters are then used in the bytes to represent the associated values of the operands. A dash indicates the operand is not used in the byte code.

Examples:

- `3A`: The literal byte `0b0011_1010`
- `-`: An ignored operand, i.e. `regA` in an instruction that only dumps to `regA`
- `x`: The value from operand `x`
  - e.g. `ADD 8` with `ADD regA,imm8 [-i: 04 i]` resolves to `04 08`
- `x+y`: value `x` added to value `y`
  - e.g. `ADD regD` with `ADD regA,reg8 [-m: 80+m]` resolves to `83`
- `x<y`: value `x` offset by `y` bits
  - e.g. `JZ $123` with `Jcc imm16 [i: 40+c i]` resolves to `68 23 01`
- `/xyz`: A ModR/M style byte where
  - `x` is the Mod, values 0..3
  - `y` is the Reg, values 0..7
  - `z` is the R/M, values 0..7
  - all three parts may be either a literal or an operand value
  - shorthand for `x<6+y<3+z`
  - e.g. `MOV regB,regC` with `MOV reg8,reg8 [rm: /3rm]` resolves to `3b`

### Instruction Example

`ins opa,opb [xy: /1xy]`

- `ins`: instruction name
- `opa,opb`: comma-separated list of operands `opa` and `opb`
- `[...]`: opcode bytes
- `xy:`: shorthand for operands; `x` resolves to `opa`, `y` resolves to `opb`
- `/1xy`: ModR/M style byte, where Mod is 1, Reg is `x`, and R/M is `y`
  - resolves to `0b01xxxyyy`

### ModR/M

Source: [Wikipedia - ModR/M](https://en.wikipedia.org/wiki/ModR/M)

> The ModR/M byte is an important part of instruction encoding for the x86 instruction set.

```
| Bit   | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| Usage |  Mod  |    Reg    |    R/M    |
```

## Processors

### i8008

- [Intel 8008 - Wikipedia](https://en.wikipedia.org/wiki/Intel_8008)
- [MCS-8 User Manual.pdf](https://en.wikichip.org/w/images/e/ec/MCS-8_User_Manual_%28Rev_4%29_%28Nov_1973%29.pdf)

- 8-bit data
- 12-bit address
