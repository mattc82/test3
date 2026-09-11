# Practice Lab Exam: Run-Length Encoder

**This is a self-study practice exam, not a real course document.** It was
put together to give you extra reps on the concepts from PA0 (argument
processing, C strings as arrays, character manipulation, function
prototypes, and simple control flow) in a fresh problem, since you can't
reuse your actual PA0 submission to study from. There is no real grader,
no real deadline, and no real academic-integrity policy attached to this
file — treat it like a self-quiz. (If you want to practice "exam
conditions," just set a timer, close your notes except the man pages, and
don't look at your PA0 code while you work.)

## 1. Introduction

In PA0 you wrote a **decoder** for run-length encoded (RLE) strings. In
this practice exam you'll write the other half of that pipeline: an
**encoder**. Given a plain string, your program should compress runs of
repeated characters into `<count><char>` pairs, exactly like the encoding
you were decoding in PA0.

Some examples of the compression, using the same convention as PA0 (every
run — even a run of length one — is written out with its count):

```
aaabbc          => 3a2b1c
abbCCC          => 1a2b3C
eeeeeeeeeeeeeeezzz => 15e3z
```

Non-letter characters are valid input too, and are compressed the same
way:

```
& ((( @         => 1&1 3(1 1@
```

## 2. Suggested Resources

To keep this close to real exam conditions, hold yourself to the same
resource limits as the real thing:

- Unix man pages (`man <topic>`) and info pages (`info <topic>`) are fair
  game.
- Don't look at your PA0 `rldecode.c` while you work — the point is to
  write the logic fresh, not transcribe it.
- No Stack Overflow, no GitHub search, no asking an AI to write the
  function bodies for you. (Asking *me* to help you set up this practice
  repo, explain a concept, or review your finished code afterward is a
  different thing entirely — just don't let a tool write the exam
  answers for you, or the practice won't do anything for you.)

## 3. Requirements

You must implement two functions in `src/rlencode.c` (prototypes are
already provided at the top of the file):

- **`int countrun(const char *str, int start)`**
  Given a string and a starting index, this function returns the number
  of times `str[start]` repeats *consecutively* beginning at that index.
  For example, if `str` is `"aaabbc"` and `start` is `0`, it should
  return `3` (three `a`s in a row starting at index 0). If `start` is
  `3`, it should return `1` (only one `b` at index 3, since index 4 is
  also `b`... look carefully at where each run actually starts before
  you assume the answer!).
  You do not need to handle `start` being out of bounds or negative.

- **`int encode(char *str)`**
  This function walks the entire string, uses `countrun()` to find each
  run, and prints the encoded result to standard output (count followed
  by character, for every run, with no separators between pairs),
  followed by a single ASCII newline. It returns the integer length of
  the *encoded* output string (not the input string) as a C integer.
  An empty input string (length 0) is considered malformed input for
  this exercise: `encode()` should print an error message to standard
  output instead of an encoded string, and return `-1`.

You are also given (already partially implemented) **`int main(int argc,
char *argv[])`**, which is responsible for command-line argument
handling. It must:

- Accept exactly one command-line argument (the string to encode).
- Any other invocation (zero, two, or more arguments) is an error: print
  a usage message and return a non-zero value from `main()`, without
  calling `encode()`.
- On a correct invocation, call `encode()` on the argument and return `0`
  if `encode()` succeeds (returns a non-negative length) or a non-zero
  value if `encode()` reports malformed input (`-1`).

## 4. Guidance

- Structure `encode()` around `countrun()`, the same way PA0's
  `decode()` was structured around `isnumber()`. If you find yourself
  writing run-detection logic inline in `encode()` instead of calling
  `countrun()`, stop and reconsider.
- `countrun()` is a plain character-array walk: start at the given
  index, and keep looking at the *next* character to see if it matches
  the one you started on. Stop as soon as it doesn't (or you hit the
  end of the string, marked by `'\0'`).
- Once you know a run's length and character, you need to print an
  integer followed by a character. `printf("%d%c", count, ch)` is fine
  to use here — the "don't use library integer-parsing functions" rule
  from PA0 was about *parsing* integers out of a string you're given,
  not about printing one you already computed. There's no parsing of
  untrusted integers happening in this assignment at all.
- To move from one run to the next inside `encode()`, remember that
  `countrun()` already told you how long the current run is — use that
  to jump your index forward instead of re-scanning character by
  character.
- Watch your string terminator. `strlen()` from `<string.h>` is fine to
  use if you want the input length up front, but you can also just walk
  until you hit `'\0'`.

## 5. Testing

A handful of test cases are provided under `tests/cases/`. Each test
`NN` consists of:

- `NN.args` — the single command-line argument to pass to your program
- `NN.expected_stdout` — the exact standard output your program should
  produce
- `NN.expected_status` — the exit status your program should return
  (`0` or `1`)

Run the whole suite with:

```
make test
```

This builds `rlencode` and runs `tests/run_tests.sh` against it. Feel
free to add your own cases to `tests/cases/` — that's good practice for
thinking about edge cases (what happens with a one-character string? A
string that's all one repeated character? A string with no repeats at
all?).

## 6. Self-Grading Rubric

There's no autograder here, so use this checklist to self-assess, out of
20 points total, mirroring how a real lab exam in this format tends to
be weighted:

| Points | Criterion |
|-------:|-----------|
| 6 | `countrun()` correctly finds run lengths for runs at the start, middle, and end of a string |
| 4 | `encode()` correctly encodes strings made of a single run |
| 6 | `encode()` correctly encodes strings with a mixture of several runs and character types (letters, symbols, spaces) |
| 4 | `main()` and `encode()` correctly detect and report the two error cases (bad argument count; empty string) with the right exit status |

## 7. Getting Your Own Copy

This folder is already a small git repository (see `git log`), so you
can clone it locally the same way you'd clone any other repo, then work
in the clone:

```
git clone /path/to/rlencode-practice-exam my-practice-copy
cd my-practice-copy
```

or, if you'd rather have it under your own GitHub account so you can
push progress commits the way you do for real assignments, create a new
empty repository on GitHub first and then:

```
cd rlencode-practice-exam
git remote add origin <your-new-empty-repo-url>
git push -u origin main
```
