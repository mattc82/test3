# Alternating Shift Cipher

Simple substitution ciphers were historically used to obscure short messages.
In this lab exam, you will implement an *alternating shift cipher*.

The cipher uses two different shifts:

- Characters at even indexes use a shift of 3.
- Characters at odd indexes use a shift of 7.

Indexes start at zero. Therefore, the first character uses a shift of 3, the
second character uses a shift of 7, the third uses a shift of 3, and so on.

Letters rotate through their own alphabet while preserving case. Digits rotate
through the decimal digits. All other characters remain unchanged.

For example:

```text
Plain text: Hello, World! 1234
Encrypted:  Klsor, Zvuso! 4567
```

The first character, `H`, has index 0, so it shifts by 3:

```text
H -> K
```

The second character, `e`, has index 1, so it shifts by 7:

```text
e -> l
```

The third character, `l`, has index 2, so it shifts by 3:

```text
l -> o
```

Digits use the same alternating shifts:

```text
1 -> 4
2 -> 9
3 -> 6
4 -> 1
```

## Academic Integrity

_**THIS IS AN EXAMINATION**_. You must follow your course’s academic integrity
policies. Use only the resources explicitly authorized by your instructor.

## Resources

You may use Unix manual pages and info pages available on the lab machines.

Useful manual pages include:

```text
man 3 isalpha
man 3 isdigit
man 3 islower
man 3 isupper
```

You may use any functions declared in `ctype.h`.

## Requirements

Implement the two functions in `src/alternating_shift.c`.

### `char shift_char(char c, int shift)`

This function shifts one character forward by `shift` positions.

It must obey these rules:

1. Uppercase letters remain uppercase.
2. Lowercase letters remain lowercase.
3. Letters wrap around from `Z` to `A` and from `z` to `a`.
4. Digits wrap around from `9` to `0`.
5. Characters that are not letters or digits remain unchanged.
6. The `shift` value may be larger than the size of a character set.

Examples:

```text
shift_char('A', 3)  returns 'D'
shift_char('Z', 3)  returns 'C'
shift_char('x', 7)  returns 'e'
shift_char('8', 5)  returns '3'
shift_char('!', 3)  returns '!'
```

### `void alternating_shift_string(char *str)`

This function encrypts the null-terminated string `str` in place.

For every character in the string:

- Use a shift of 3 if its index is even.
- Use a shift of 7 if its index is odd.
- Use `shift_char()` to transform the character.

The null terminator (`'\0'`) must remain at the end of the string.

Examples:

```text
Input:  "ABC"
Output: "DJF"

Input:  "Zebra 98!"
Output: "Cleyh 21!"

Input:  "a-b"
Output: "d-e"
```

## Guidelines

You should use `shift_char()` inside `alternating_shift_string()`.

You can convert a character to its zero-based position by subtracting the
first character in the relevant set:

```c
'A' - 'A' == 0
'B' - 'A' == 1
'a' - 'a' == 0
'7' - '0' == 7
```

The modulus operator (`%`) is useful for wrapping around a character set.

For example, shifting `Z` by 3:

```c
('Z' - 'A' + 3) % 26
```

This produces the zero-based position of `C`.

Digits work similarly, except there are 10 digits instead of 26 letters.

## Testing

Run all provided tests from the top-level directory:

```sh
make test
```

You may write additional tests while developing.

## Submission

Submit only:

```text
src/alternating_shift.c
```

## Grading

| Category | Points |
|---|---:|
| Single-character shifting with `shift_char()` | 8 |
| Alternating shifts for simple strings | 6 |
| Mixed letters, digits, punctuation, and spaces | 6 |
| **Total** | **20** |
