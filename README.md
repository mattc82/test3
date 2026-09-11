# PacketRot Decoder

This exam implements a small command-line decoder for a compact packet format.

A packet is made of one or more *runs*. Each run has this format:

```text
count:character
```

- `count` is a positive decimal integer from 1 through 99.
- `:` separates the count from its character.
- `character` is one non-colon ASCII character.

For example:

```text
3:a2:Z4:7
```

contains three runs:

```text
3:a
2:Z
4:7
```

The packet is decoded by repeating each character the specified number of
times. Before printing each repeated character, transform it with ROT13:

- Uppercase and lowercase letters rotate by 13 positions.
- Digits rotate by 5 positions.
- All other characters remain unchanged.

Therefore:

```text
Input:  3:a2:Z4:7
Output: nnnMM2222
```

The packet `1:H1:e1:l1:l1:o` decodes to:

```text
Uryyb
```

## Academic Integrity

_**THIS IS AN EXAMINATION**_. Follow your instructor’s academic-integrity
rules and use only the resources specifically authorized for this exam.

## Resources

You may use Unix manual pages and info pages on the lab machines.

Potentially useful manual pages:

```text
man 3 putchar
man 3 isupper
man 3 islower
man 3 isdigit
```

You may use functions from `ctype.h` for `rot13_char()`.

You may **not** use `atoi()`, `strtol()`, `sscanf()`, or another library
function that parses a number from a string.

## Program Invocation

Your program must be run with exactly one packet argument:

```sh
./packetrot 3:a2:Z4:7
```

A successful invocation prints the decoded message followed by one newline and
returns zero.

Invalid invocations or malformed packets must print an error message to
standard error and return a nonzero value.

## Valid Packet Rules

A valid packet must meet all of these requirements:

1. It contains at least one run.
2. Every run begins with one or two ASCII digits.
3. Each count is from `1` through `99`.
4. A count may not begin with `0`.
5. Each count is followed immediately by a colon (`:`).
6. Each colon is followed by exactly one non-colon character.
7. The end of one run is immediately followed by the count of the next run.

Examples of valid packets:

```text
1:A
3:a2:Z4:7
2:-1:!3:0
12:x
```

Examples of invalid packets:

```text
                    empty packet
0:a                 zero is not a valid count
03:a                counts may not begin with zero
100:a               counts may not exceed 99
2a                  missing colon and value
2:                  missing value
a                   missing count
1::                 colon cannot be used as a value
```

## Requirements

Implement all functions in `src/packetrot.c`.

### `bool is_ascii_digit(char c)`

Return `true` only when `c` is an ASCII digit from `'0'` through `'9'`.

You must implement this yourself using character comparisons. Do not use
`isdigit()` in this function.

Examples:

```c
is_ascii_digit('0')  /* true */
is_ascii_digit('9')  /* true */
is_ascii_digit('a')  /* false */
is_ascii_digit('-')  /* false */
```

### `char rot13_char(char c)`

Transform one character according to these rules:

- Uppercase letters rotate by 13 positions.
- Lowercase letters rotate by 13 positions.
- Digits rotate by 5 positions.
- Other characters do not change.

Examples:

```c
rot13_char('A')  /* 'N' */
rot13_char('z')  /* 'm' */
rot13_char('7')  /* '2' */
rot13_char('!')  /* '!' */
```

### `int decode_packet(char *str)`

Validate and decode a packet string.

For a valid packet:

1. Decode every run.
2. Apply `rot13_char()` to the run’s character.
3. Print the transformed character the requested number of times.
4. Print one newline after the complete decoded message.
5. Return the length of the decoded message.

For an invalid packet:

1. Print an error message to standard error.
2. Return `-1`.
3. Do not print a partial decoded message.

### `int main(int argc, char *argv[])`

Your program must accept exactly one packet argument.

- With one valid argument, call `decode_packet()` and return zero.
- With no arguments or more than one argument, print an error message to
  standard error and return a nonzero value.
- If `decode_packet()` returns `-1`, return a nonzero value.

## Guidance

ASCII digits are consecutive, so you can convert one digit character to an
integer value like this:

```c
int value = c - '0';
```

For a two-digit count, such as `"27"`, you can build the integer one digit at
a time:

```c
count = count * 10 + (str[index] - '0');
```

To rotate a letter with wraparound, convert it to a zero-based position,
rotate it, and convert it back:

```c
'A' + (c - 'A' + 13) % 26
```

## Testing

Run the provided test suite from the top-level directory:

```sh
make test
```

A successful test run ends with:

```text
All tests passed.
```

## Submission

Submit only:

```text
src/packetrot.c
```

## Grading

| Category | Points |
|---|---:|
| `is_ascii_digit()` correctly identifies ASCII digits | 3 |
| `rot13_char()` correctly transforms letters, digits, and punctuation | 5 |
| `decode_packet()` validates and decodes valid packet strings | 7 |
| Invalid-packet handling and `main()` argument processing | 5 |
| **Total** | **20** |
