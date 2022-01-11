# The `sam` command language

This is my note of A Tutorial for the sam Command Language, by Rob Pike.

Nomenclature: **sam** keeps a copy of the text it is editing which is called a
*file*. The permanent storage on disc is called *Unix file*.

## Text

- `a`: add
- `c`: replace
- `p`: print
- `u`: undo

## Addresses

An address selects a region in the file -- a substring -- and therefore must
define the beginning and the end of a region.

Addresses may be combined with a comma:
- `13,15`: selects line 13 through line 1
- `0,13`: selects from the beginning of the file to line 13
- `.,$`: selects from the current text to the end of the file
- `0,$`: selects the whole file

Theses all all *absolute* addresses. There are also relative addresses.
- `/Emacs/` or `+/Emacs/` or `0+/Emacs/`: finds the first occurrence of
  **Emacs** searching forwards from dot.
- `-/Emacs/`: find the first occurrence of **Emacs** searching backwards from dot.
- `$-/Emacs/`: find the last occurrence in the file
- `0/Emacs/,$-/Emacs/`: selects the text from the first to last **Emacs**, inclusive.
- `/Emacs/+/Emacs`: selects the second **Emacs** followin dot.
- `-2`: selects the second previous line
- `+5`: selects the fifth following line

The `+-` idiom: selects the line before the line after the end of dot, that is ,
the complete line containing the end of dot.

## Loops

The `x` command is a looping construct: for each match of a regular expression, 
it extracts (sets dot to) the match and runs a command.

`x`(extract) syntax: `x/pattern/command`. For example, `0,$x/Emacs/vi/`: changes
**Emacs** to **vi** which means "find all occurrences of Emacs in the file, and
for each one, set the current text to the occurrence and run the command
`c/vi/`, which will replace the current text by vi."

The dafault address is dot instead of the whole page (`0,$`).

A single `u` command undos an `x` command, regardless of how many indivisual
changes the `x` makes.

The `p` command only prints the selects region. If want display more context,
use `+-`. For example `0,$x/Emacs/p` vs. `0,$x/Emacs/+-p`.

`,x/Emacs//{TM}/d`: for each occurrence of Emacs, fidn the {TM} that follows,
and delete it.

Example, turn every newline into two new line:
- `,x/\n/a/\n/`
- `,x/\n/c/\n\n/`
- `,x/$/a/\n/`
- `,x/^/i/\n/`
The last example is slightly different, because it puts a newline before each
line; the other examples place it after. The first two examples manipulate
newlines directly; the last two use regular expressions: $ is the empty string
at the end of a line, while ˆ is the empty string at the beginning.
These
solutions all have a possible drawback: if there is already a blank line (that
is, two consecutive newlines), they make it much larger (four consecutive
newlines). A better method is to extend every group of newlines by one:
`,x/\n+/a/\n/` which the regex operator `+` means "one or more".

Example, indent a block of text by a 4 spaces (the black text in slashes are 4 spaces).
- `,x/^/a/    /`
- `,x/^/c/    /`
- `,x/.*\n/i/    /`
The last use `.*\n` to match lines: `.*` matches the longest possible string of
non-newline characters. And delete the indentation is just easy: `,/x^    /d`.

## Conditionals

`g` command's syntax: `g/pattern/command` runs the command if dot contains a
match of the pattern without changing the value of dot. `v` command is the
reverse.

Difference between `x` and `g`:
- `,x/Emacs/c/vi/`: changes each occurrence of the word **Emacs** in the file to
  the word **vi**.
- `,g/Emacs/c/vi/`: changes the whole file to **vi** if there is the word
  **Emacs** anywhere in the file.

## Composition

`,x/Emacs/ c/emcas/` == `,x/Emacs/x/E/ c/e/`. (blanks can be uesd to separate
commands on a line to make them easier to read).

An example presented above, printing all lines in the file containing the word **Emacs**:
`,x/.*\n/g/Emacs/p`.

To save typing, because `.*\n` is a common pattern in x commands, if the x is
followed immediately by a space, the pattern `.*\n` is assumed. Therefore, the
above could be written more succinctly: `,x g/Emacs/p`.

- `,x g/Emacs/p`: is conceptually cleaner but slower
- `,x/Emacs/+-p`: is easier to type and faster, but odd.

Example, given the text:
```
command name="append-to-file", key="[unbound]"
Takes the contents of the current buffer and appends it to the
named file. If the file doesn’t exist, it will be created.

command name="apropos", key="ESC-?"
Prompts for a keyword and then prints a list of those commands
whose short description contains that keyword.  For example,
if you forget which commands deal with windows, just type
"@b[ESC-?]@t[window]@b[ESC]".

and so on
```
find the description of "apropos" command: 
```
,x/(.+\n)+/g/command name="apropos"/p
```
`(.+\n)+` matches one or more lines with one or more characters each. So,
`,x/(.+\n)+/` extracts each description, then `g/command name="apropos"/`
selects the description for "apropos" and `p` print it.

Example, rename a variable `n` to `num` in a C program.
- bad attempt: `,x/n/c/num/` which chagnes all `n`'s in the file.
- better: `,x/[a-zA-Z_][a-zA-Z_0-9]*/ g/n/ v/../ c/num/`
A C identifier is an alphabetic or underscore followed by zero or more
alphanumerics or underscores. So `,x/[a-zA-Z_][a-zA-Z_0-9]*` selects all
identifiers, then 'g' selects those that contains `n`, and `v` is a trick: it
rejects those identifiers containing more than one character. Hence the `c/num/`
applies only to free-standing n’s.

However, the above method will match `\n`. We can use `y` command to fix that.
`y` command's syntax: `/y/pattern/command` runs the command on the pieces of
text between matches of the pattern; if `x` selects, `y` rejects. The final
version is: `,y/\\n/ x/[a-zA-Z_\=][a-zA-Z_0-9]*/ g/n/ v/../ c/num`.

## Grouping

By enclosing commands in brackets `{}`, commands may be applied in parallel.

Example, report on appearences of **Emacs** in a file: 
```
,x/Emacs/{
    =
    +-p
}
```
where `=` command reports the line and character numbers of dot.

Example, swap **Emacs** and **vi**
```
,x/Emacs/vi/{
    g/Emacs/ c/vi/
    g/vi/ c/Emacs/
}
```
or
```
,x/[a-zA-Z]+/{
    g/Emacs/ v/....../ c/vi/
    g/vi/ v/.../ c/Emacs/
}
```
to make sure we don't change strings embedded in words.

## Multiple Changes

You might wonder why, once **Emacs** has been changed to **vi** in the
aboveexample,the second command in the braces doesn’t put it back again. The
reason is thatthe commands are run in parallel: within any top-level sam
command, all changesto the file refer to the state of the file before any of the
changes in thatcommand are made. After all the changes have been determined,
they are allapplied simultaneously.

## A few other text comands

`m` commands moves the current text to after the text specified by the
(obligatory) address after the command. `/Emacs/+-m 0` moves the next line
containing **Emacs** to the beginning of the file.

`t` copies the text: `/Emacs/+-t0`

`s` command's syntax: `s/pattern/replacement/` which finds the first occurrence
of the pattern and replaces it by replacement text, leaving dot set to the
entire address of the substitution.

Example, given text
```
This manual is organized in a rather haphazard manner.  The first
```
`s/haphazard/thoughtless/` will change it to
```
This manual is organized in a rather thoughtless manner.  The first
```
`s/T/"&&&&"/` will change it to
```
"TTTT" manual is organized in a rather thoughtless manner.  The first
```
where `&` stands for the text matching the pattern.

`s2/is/was/`: a number may be specified after `s` to indicate which occurrence
of the pattern to substitude; the default is the first:
```
"TTTT"his manual was organized in a rather thoughtless manner. The first
```

Global replacement is achieved by `g`: `s/[a-zA-Z]/x/g`:
```
"xxxx"xxx xxxxxx xxx xxxxxxxxx xx x xxxxxx xxxxxxxxxxx xxxxxxx xxx xxxxx
```

## Files

`f`: list current file.
- `-`: each file has a leading `-`
- '+': `-` becomes `+` if it is has an opening window
- `.`: identify current file
- `` ` ``: a leading `` ` `` means the file is modified

'X': simliar to `x` but run command in each file whose menu entry (that is,
whose line printed by an `f` command) matches the pattern. For example, since an
apostrophe identifiers modified files, ``X/`/w`` writes the changed files out to
disc.

Example, find all uses of a particular variable in the C source files:
`X/\.c$/ ,x/variable/+-p` we can use a `f` command to identify which file the variable appears in:
```
X/\.c$/ ,g/variable/ {
    f
    ,x/variable/+-{
        =
        p
    }
}
```

`X D` cleans up for a fresh start.
