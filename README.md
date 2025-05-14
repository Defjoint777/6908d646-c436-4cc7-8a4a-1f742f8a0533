rm da<!---
{
  "depends_on": ["https://github.com/STEMgraph/e3a451cf-dccf-474b-97d2-17505e309ba0"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-04-01",
  "keywords": ["GAS", "syscall", "read", "write", "file descriptor", "ASCII", "Linux"]
}
--->

# Reading, Modifying, and Printing a Character with GAS (GNU Assembly)

## 1) Introduction

In [previous exercises](https://github.com/STEMgraph/e3a451cf-dccf-474b-97d2-17505e309ba0), we explored how to print text and exit a program using the `write` syscall. Now, we will introduce another essential syscall: `read`.

In this task, your assembly program will:

1. Open a file (`./assets/data`)
2. Read a single byte
3. Increment the ASCII value of the byte
4. Write the result to the terminal

This teaches basic **file I/O** and **byte-level manipulation**.

---

## Required Syscalls

| Syscall     | Number | Purpose       |
|-------------|--------|---------------|
| `open`      | 2      | Open a file   |
| `read`      | 0      | Read from a file descriptor |
| `write`     | 1      | Write to a file descriptor  |
| `exit`      | 60     | Exit program  |

Use `O_RDONLY = 0` when opening the file.

---

## Example Code Skeleton (GAS)

```gas
    .section .data
path:
    .string "./assets/data"
buffer:
    .byte 0

.section .text
.globl _start

_start:
    # open(path, O_RDONLY)
    mov $2, %rax                 # syscall: open
    lea path(%rip), %rdi         # filename pointer
    xor %rsi, %rsi               # flags = O_RDONLY
    syscall
    mov %rax, %r12               # save file descriptor

    # read(fd, buffer, 1)
    mov $0, %rax                 # syscall: read
    mov %r12, %rdi               # fd
    lea buffer(%rip), %rsi       # destination
    mov $1, %rdx                 # number of bytes
    syscall

    # increment the byte
    incb buffer(%rip)

    # write(1, buffer, 1)
    mov $1, %rax                 # syscall: write
    mov $1, %rdi                 # stdout
    lea buffer(%rip), %rsi       # source
    mov $1, %rdx                 # number of bytes
    syscall

    # exit(0)
    mov $60, %rax
    xor %rdi, %rdi
    syscall
```

Ensure that the file `./assets/data` exists and contains **exactly one character**.

---

## 2) Tasks

1. **Create Input File**: Use `echo -n A > ./assets/data` to prepare the file.
2. **Assemble & Link**: Use `as`, `ld`, and run the binary. The output should be the next ASCII character.
3. **Test Edge Values**: What happens with characters like `Z`, `9`, or `~`?
4. **Bonus**: Modify the program to read two bytes and increment both.

---

## 3) Questions

1. What does the `open` syscall return? Why do we store it in `%r12`?
   we store it in %r12 to dont overrite it with syscall so the %r12 can be used by read.
3. Why is `O_RDONLY` represented as `0`?
   cuz readonly only opens the file to read like 0 is doing.
4. How would you modify the loop to increment multiple bytes?
   while buffer.length != messege.length {buffer ++:}
5. What if the file doesn't exist — how does the system respond?
   No respond at all
7. What's different about incrementing ASCII `'A'` vs `'9'`?
   a = 01100001 = 97  b = 01100010 (+1 bit)  9 = 00111001 = 57  : = 58 =00111010 its all about ASCII

<details>
  <summary>Hint: ASCII Table</summary>

  Use [ASCII Table Reference](https://www.asciitable.com/) to understand what values you're manipulating.
</details>

---

## 4) Advice

Assembly offers **total control**, but demands **total clarity**. You must explicitly track every register and memory access. When working with syscalls, always double-check your syscall number and argument order.

Use the shell to make quick test files:

```bash
echo -n A > ./assets/data
```
