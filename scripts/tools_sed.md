# Linux Shell Practice: sed, sort, uniq, awk, and More

Spreadsheets are for people with a mouse. This chapter is a workout on `banklist.csv`: failed US banks, one row at a time. You will cut columns, drop junk, sort, uniq, and pipe until a number comes out.

**About 45 minutes. Open this when you have a CSV.** Done when a pipeline prints a count you can check with your eyes.

[← Two-hour path](../README.md#if-you-are-new) | [Next, if you have a log: awk →](tools_awk.md)

---

## Commands to Practice

You already met these. Today they work as a band.

- `pwd`, `cd`, `chmod`, `chown`, `ls` (with flags), `rm`, `mkdir`, `tail`, `head`, `<`, `grep`, `|`

---

## File exploration with `banklist.csv`

The file `banklist.csv` sits in this folder. It is real FDIC failure data, which is either grim or excellent practice, depending on your relationship with banking. Open it with `head` before you `sed` it; columns lie if you have not looked.

---

## Viewing File Contents

`head` and `tail` take a filename. `cat file | head` works; `head file` is the same walk with fewer steps. Try both, then keep the shorter one.

- **Print first 5 lines:**

  ```sh
  head -5 < banklist.csv
  cat banklist.csv | head -5
  ```

If a chat window counted the banks, run the same filter here. When the two numbers disagree, believe `wc -l`, and check whether the header got counted.

- **Print last 5 lines:**

  ```sh
  cat banklist.csv | tail -5
  ```

- **Print the 2nd last line:**

  ```sh
  tail -2 banklist.csv | head -1
  ```

  `tail -2` is the last two lines; `head -1` keeps the first of those. Unix does not have `tail --second-to-last`. It has pipes.

- **Print specific columns using cut:**

  ```sh
  cat banklist.csv | cut -d , -f 1
  # Print the first column of a comma delimited file
  ```

  `-d ,` is the delimiter. Count the commas in `head -1` if field 1 is not the bank name you expected.

---

## Removing Lines with Leading Quotes Using `sed`

`sed` is a stream editor. It reads a line, maybe changes it, prints it. The file on disk stays put unless you pass `-i`. Some rows in this CSV start with a stray `"`. Those are junk. The scrubbed file is the one you sort.

On your own laptop, find-and-replace is often easier with [`sd`](modern_tools.md#sd-find-and-replace): `sd -p 'old' 'new' file` previews, and a later run without `-p` rewrites the file. Keep using `sed` for this chapter. It is the tool on the server, in the script, and in the pipeline below. Leave `sed` itself unaliased.

- [sed one-liners reference](http://sed.sourceforge.net/sed1line.txt)
- **Print only lines which do NOT match regexp (emulates `grep -v`):**
  - Method 1:

    ```sh
    sed -n '/regexp/!p'
    ```

  - Method 2 (simpler syntax):

    ```sh
    sed '/regexp/d'
    ```

  `!p` is "print if it does *not* match." `d` is "delete that line from the stream" (the file on disk is fine unless you used `-i`).

- **Remove all lines with leading quotes (`^"`):**

  ```sh
  sed -n '/^"/!p' < banklist.csv > banklist-scrubbed.csv
  ```

  `^` means start of line. The output file is new; the original stays. Look at both with `head` before you trust the scrub.

---

## Sorting Bank Names

`sort` thinks in lines, not in "bank names" as a concept. `-f` is "Bank" and "bank" are the same argument.

- **Sort ascending and descending using the `sort` command.**
- **Options:**
  - Try ascending and then descending
  - Try ignoring lowercase/uppercase (use `sort -f`)

  ```sh
  sort banklist-scrubbed.csv           # Ascending
  sort -r banklist-scrubbed.csv        # Descending
  sort -f banklist-scrubbed.csv        # Ignore case
  ```

---

## Other Useful Commands

- **Print the last 5 and the first 5 lines:**

  ```sh
  head -5 banklist.csv
  tail -5 banklist.csv
  ```

- **Count the number of banks (lines):**

  ```sh
  wc -l banklist.csv
  ```

  That count includes the header. The header is not a bank. Subtract one in your head, or notice it on purpose.

- **Remove duplicates and count:**

  ```sh
  uniq banklist-scrubbed.csv | wc -l
  wc -l banklist-scrubbed.csv
  ```

- **Why are counts the same?**

  > If there are no duplicate lines, both counts will match. Also: `uniq` only collapses *adjacent* duplicates. Without `sort` first, it will lie to you with a straight face. Models forget that too.

---

## States List and Sorting

Column 3 is the state — on *this* CSV. Count the commas with `head -1` if you do not believe me.

- **Print out the list of all States (unique, sorted):**

  ```sh
  cut -d , -f3 banklist-scrubbed.csv | sort -f | uniq
  ```

---

## Filtering and Counting by State

The commas around `AZ` keep you from matching a bank in BLAZING-whatever. CSV is not a database; it is a polite agreement about commas.

- **Get the list of all banks in AZ:**

  ```sh
  grep ',AZ,' banklist-scrubbed.csv
  ```

- **Count of all banks in AZ:**

  ```sh
  grep ',AZ,' banklist-scrubbed.csv | wc -l
  ```

---

## Combining Tools (Pipes)

This is the whole chapter in one line: cut a column, sort it, collapse duplicates, count.

- **Count unique states:**

  ```sh
  cut -d , -f3 banklist-scrubbed.csv | sort | uniq | wc -l
  ```

- **Find banks with a specific pattern and count:**

  ```sh
  grep 'SAVINGS' banklist-scrubbed.csv | wc -l
  ```

---

## AWK Basics (for CSV and Logs)

A preview of the next chapter. `-F,` tells AWK the comma is the field separator, not whitespace. `$1` is the bank, `$3` is the state — same columns you just `cut`.

- **Print the first column (bank name):**

  ```sh
  awk -F, '{print $1}' banklist-scrubbed.csv
  ```

- **Print banks in AZ:**

  ```sh
  awk -F, '$3=="AZ" {print $1}' banklist-scrubbed.csv
  ```

- **Count banks per state:**

  ```sh
  awk -F, '{count[$3]++} END {for (s in count) print s, count[s]}' banklist-scrubbed.csv | sort
  ```

  `END` runs once, after the last line. The next chapter lives here.

---

## Checking File Encoding and Line Endings

Windows leftover `CRLF` makes `$` in a regex miss the end of the line, and makes you question your life choices. `file` tells on it.

- **Check file encoding:**

  ```sh
  file banklist.csv
  ```

- **Check for Windows line endings:**

  ```sh
  file banklist.csv | grep CRLF
  ```

---

## Comparing File Line Counts

The scrubbed file should be shorter if you actually dropped lines. If the counts match, either there were no leading quotes or the redirect wrote the same file twice. Look.

- **Compare the count in the 2 files:**

  ```sh
  wc -l banklist.csv
  wc -l banklist-scrubbed.csv
  ```

---

## Bash Script: Line Count Difference

Create a script that accepts the 2 files as arguments and prints the difference in the line counts. This is the whole job, in miniature: two numbers, a check that you got two arguments, and no AI-generated CSV parser required. Put it in `scratch/` if you like; that folder is gitignored on purpose.

### Steps

1. Store the count of 1st file in a variable
2. Store count of 2nd file in a variable
3. Print the difference

- The script should error out if arguments are not 2.
- Call the script `linecount`.

---

## More Resources

- [Linux Journey](https://linuxjourney.com/) – More Linux learning resources
- [Digital Ocean Linux](https://www.digitalocean.com/community/tags/linux-basics)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [sed One-Liners Explained](https://catonmat.net/sed-one-liners-explained-part-one)

---

[Next: AWK Tutorial →](tools_awk.md)
