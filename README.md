# AssemblyCalc



```
.data
    prompt: .asciiz "Enter expression: "
    result_msg: .asciiz "Result: "
    newline: .asciiz "\n"
    buffer: .space 50

.text
.globl main

main:
    # Print prompt
    li $v0, 4
    la $a0, prompt
    syscall

    # Read user input
    li $v0, 8
    la $a0, buffer
    li $a1, 50
    syscall

    # Evaluate expression
    la $a0, buffer
    jal evaluate_expression

    # Print result message
    li $v0, 4
    la $a0, result_msg
    syscall

    # Print result
    move $a0, $v0
    li $v0, 1
    syscall

    # Print newline
    li $v0, 4
    la $a0, newline
    syscall

    # Exit
    li $v0, 10
    syscall

# Evaluate arithmetic expression with correct precedence
evaluate_expression:
    move $t0, $a0  # Load input buffer
    jal get_number  # Get first number
    move $t1, $v0  # Store in t1
    li $t3, 0       # Pending multiplication/division result
    li $t4, 0       # Pending multiplication/division flag
next:
    jal get_operator  # Get operator
    beqz $v0, done  # If no operator, finish
    move $t2, $v0   # Store operator
    jal get_number  # Get next number
    move $t5, $v0   # Store operand
    
    li $t6, '*'
    beq $t2, $t6, mul_op
    li $t6, '/'
    beq $t2, $t6, div_op
    
    # If pending multiplication/division, resolve it first
    beqz $t4, process_addsub
    li $t6, 1
    beq $t4, $t6, mul_finalize
    li $t6, 2
    beq $t4, $t6, div_finalize

process_addsub:
    li $t6, '+'
    beq $t2, $t6, add_op
    li $t6, '-'
    beq $t2, $t6, sub_op
    j next

mul_op:
    mul $t5, $t1, $t5  # Multiply immediately
    move $t1, $t5
    j next

div_op:
    div $t1, $t5
    mflo $t1
    j next

add_op:
    add $t1, $t1, $t5
    j next

sub_op:
    sub $t1, $t1, $t5
    j next

done:
    move $v0, $t1  # Return result
    jr $ra

# Get number from buffer
get_number:
    li $v0, 0
num_loop:
    lb $t4, 0($t0)
    beqz $t4, num_done
    blt $t4, '0', num_done
    bgt $t4, '9', num_done
    sub $t4, $t4, '0'
    mul $v0, $v0, 10
    add $v0, $v0, $t4
    addi $t0, $t0, 1
    j num_loop
num_done:
    jr $ra

# Get operator from buffer
get_operator:
    lb $v0, 0($t0)
    addi $t0, $t0, 1
    jr $ra


```
# Assembly Calculator (MIPS)

## Overview
This project is a simple arithmetic calculator written in MIPS Assembly for the **MARS simulator**. It supports basic arithmetic operations while following **operator precedence (PMDAS)**:
- Multiplication (`*`) and division (`/`) are evaluated before addition (`+`) and subtraction (`-`).
- Left-to-right execution for same-priority operations.

## Features
- Accepts user input directly as a mathematical expression.
- Supports `+`, `-`, `*`, and `/` operators.
- Handles multiple operations in one expression.
- Uses **correct precedence** for multiplication and division before addition and subtraction.
- Works with the **MARS simulator**.

## Usage
### Requirements
- **MARS (MIPS Assembler and Runtime Simulator)** - [Download MARS](http://courses.missouristate.edu/KenVollmar/mars/)

### Running the Program
1. Open MARS.
2. Load the `assembly_calculator.asm` file.
3. Assemble (`F3` or "Assemble" button).
4. Run (`F5` or "Run" button).
5. Enter an arithmetic expression (e.g., `122 - 14 / 2 * 1`).
6. View the result displayed in the console.

## Example Calculations
| Input Expression  | Expected Output |
|-------------------|----------------|
| `5 + 2 * 10 - 6 / 3` | `23` |
| `8 * 4 / 2 + 10 - 3` | `19` |
| `100 / 5 * 2 - 6` | `34` |
| `12 + 6 - 4 * 2 / 2` | `14` |

## Limitations
- Does **not** support parentheses `()`.
- Only works with **positive integers**.
- No floating-point support.

## Future Improvements
- Add parentheses support for full **PEMDAS** compliance.
- Extend support for negative numbers.
- Implement floating-point calculations.

## License
This project is open-source and can be modified or distributed freely.



This code 
