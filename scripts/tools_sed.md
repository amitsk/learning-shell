# Linux Shell Practice: sed, sort, uniq, awk, and More

Spreadsheets are for people with a mouse. This chapter is a workout on `banklist.csv`: failed US banks, one row at a time. You will cut columns, find out why commas lie inside quotes, sort, uniq, and pipe until a number comes out.

**About 45 minutes. Open this when you have a CSV.** Done when a pipeline prints a count you can check with your eyes.

[← Two-hour path](../README.md#if-you-are-new) | [Next, if you have a log: awk →](tools_awk.md)

---

## Commands to Practice

You already met these. Today they work as a band.

- `pwd`, `cd`, `chmod`, `chown`, `ls` (with flags), `rm`, `mkdir`, `tail`, `head`, `<`, `grep`, `|`

---

## File exploration with `banklist.csv`

The file `banklist.csv` sits in this folder. It is real FDIC failure data, which is either grim or excellent practice, depending on your relationship with banking. Open it with `head` before you `sed` it; columns lie if you have not looked.

Shape of the thing, so you can check your work: **572 lines**, one header, so 571 banks. Line endings are Windows **CRLF**. Forty-one rows contain quoted fields, because a bank name with a comma needs quotes to stay one column. Those 41 rows are the reason this chapter has a plot.

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

## Quoted fields, and why `cut` lies

`sed` is a stream editor. It reads a line, maybe changes it, prints it. The file on disk stays put unless you pass `-i`.

Before you sed anything, count the distinct values in column 3:

```sh
cut -d , -f3 banklist.csv | sort -f | uniq | wc -l
```

You should see **81**. There are not 81 US states. Some of those values are cities, because a CSV field can contain its own delimiter when it is wrapped in quotes: `"Some Bank, N.A."` is one field, not two. Forty-one rows in this file start with a quote for exactly that reason.

Those rows are not junk. They are the file being honest about commas. `cut -d,` counts commas; it does not parse CSV. That is the difference.

Delete the quoted rows anyway, and watch what it costs:

```sh
grep -c ',AZ,' banklist.csv            # 16 banks in Arizona
sed -n '/^"/!p' < banklist.csv > banklist-scrubbed.csv
grep -c ',AZ,' banklist-scrubbed.csv   # 11. You just deleted five real banks.
```

`^` anchors to start of line, and `!p` means "print if it does *not* match" (shorter equivalent: `sed '/^"/d'`, which deletes matching lines from the stream without touching the input file unless you pass `-i`). That drops 41 rows: 572 lines become 531. Keep the original; use `banklist-scrubbed.csv` for the column exercises below so column 3 is actually the state.

On your own laptop, interactive find-and-replace is nicer with [`sd`](modern_tools.md#sd-find-and-replace) (`sd -p 'old' 'new' file` previews first). Keep `sed` for scripts, servers, and this chapter, and leave `sed` unaliased. Reference: [sed one-liners](https://sed.sourceforge.io/sed1line.txt).

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

## Counting Lines and Duplicates

- **Count lines (banks + header):**

  ```sh
  wc -l banklist.csv                    # 572 (includes the header — not a bank)
  uniq banklist-scrubbed.csv | wc -l    # 531
  wc -l banklist-scrubbed.csv           # 531
  ```

  Both scrubbed counts match because no two rows are byte-for-byte identical. Remember: `uniq` only collapses *adjacent* duplicates, so without `sort` in front it will miss repeats without a hint of remorse.

---

## States, Filtering, and Pipes

On `banklist-scrubbed.csv`, column 3 is the state (commas around `,AZ,` keep `grep` from matching `BLAZING`):

```sh
# Unique sorted states (45 lines: 44 states/territories + the header)
cut -d , -f3 banklist-scrubbed.csv | sort -f | uniq
cut -d , -f3 banklist-scrubbed.csv | sort -f | uniq | wc -l   # 45; add `tail -n +2` first to drop the header

# Banks in Arizona (11 in scrubbed vs. 16 in the original)
grep ',AZ,' banklist-scrubbed.csv
grep -c ',AZ,' banklist-scrubbed.csv

# Case-insensitive match (34; drop -i and you get 0 because the file says `Savings`, not `savings`)
grep -ic 'savings' banklist-scrubbed.csv
```

---

## What an LLM does with these tools

Ask an agent "how many Arizona banks failed?" and it does not read the file. It writes a pipeline — usually something like this — runs it, and reports the number:

```sh
cut -d, -f3 banklist.csv | grep AZ | wc -l
```

That line is wrong, for the reason this chapter has already shown you: `cut` splits quoted bank names, so five Arizona banks never reach `grep`. It prints **11**. The real count is **16**. (`grep AZ` is a second hazard — it matches those letters anywhere — which is why the chapter uses `',AZ,'`.) The model will still sound certain. The number is the tell.

It will also generate `sed -n '/^"/!p'` to "clean" a CSV, because deleting the awkward rows makes the next command work. Ask what it deleted before you accept the clean file. The pipeline is the receipt; the chat transcript is a rumor.

Your job is not to type the pipeline faster. It is to run the same one and look at the file when the answers disagree. An agent's command is a hypothesis. `wc -l` is the experiment.

---

## AWK Basics (for CSV and Logs)

A preview of the next chapter. `-F,` tells AWK the comma is the field separator, not whitespace. `$1` is the bank, `$3` is the state — same columns you just `cut`, with the same caveat. This works on the scrubbed file. On the original, a quoted bank name still contains the comma AWK is splitting on, and `$3` becomes a city.

- **Print the first column (bank name):**

  ```sh
  awk -F, '{print $1}' banklist-scrubbed.csv
  ```

- **Print banks in AZ:**

  ```sh
  awk -F, '$3=="AZ" {print $1}' banklist-scrubbed.csv
  ```

  Eleven rows. Same number as `grep`. That is the point of checking.

- **Count banks per state:**

  ```sh
  awk -F, '{count[$3]++} END {for (s in count) print s, count[s]}' banklist-scrubbed.csv | sort
  ```

  `END` runs once, after the last line. The header shows up as its own "state" with a count of 1. The next chapter lives here.

---

## When not to use the shell for this

Shell pipelines are excellent at line-shaped text and bad at structure. The moment a field can contain the delimiter, quotes can nest, or the file is JSON with commas inside strings, stop and use a parser.

```sh
# 44 (43 states + PR), skipping the header, no scrubbing, no lies
python3 -c 'import csv,sys; r=csv.reader(sys.stdin); next(r); [print(row[2]) for row in r]' < banklist.csv | sort -u | wc -l
```

Rules of thumb:

- **Line-oriented text** → `awk`, `sed`, `cut`. This chapter.
- **Fields that can contain the delimiter** → Python's `csv` module, SQLite, or a dedicated tool. Not `cut`.
- **JSON** → `jq`.
- **YAML** → `yq`.

The shell is a glue language, not a CSV parser. Knowing where the line is saves you from debugging a pipeline that works on every file except the real one.

---

## Checking File Encoding and Line Endings

Windows leftover `CRLF` makes `$` in a regex miss the end of the line, and makes you question your life choices. `file` usually tells on it. On this copy, `file` stays quiet, which is why you check the bytes instead of trusting the label.

- **Check file encoding:**

  ```sh
  file banklist.csv
  ```

- **Check for Windows line endings:**

  ```sh
  grep -c $'\r$' banklist.csv     # 572 — every line ends CRLF
  cat -A banklist.csv | head -3   # each line ends in ^M$
  ```

  `$'\r'` is Bash's way of writing a carriage return. `$` anchors it to the end of the line, so this counts lines that actually have one. `cat -A` shows the `^M` so your eyes can confirm what the count claimed.

- **Strip them, if you want the easy regex life:**

  ```sh
  sed 's/\r$//' banklist.csv > banklist-unix.csv
  grep -c $'\r$' banklist-unix.csv   # 0
  ```

  Now `$` in a regex means what you thought it meant. That is one fewer mystery before lunch.

---

## Comparing File Line Counts

The scrubbed file is shorter because you actually dropped lines. The difference is the number of quoted rows you deleted: **41**. If the counts match, either there were no leading quotes or the redirect wrote the same file twice. Look.

- **Compare the count in the 2 files:**

  ```sh
  wc -l banklist.csv
  wc -l banklist-scrubbed.csv
  ```

  **572** and **531**. Subtract them and you get 41, the number of rows with quoted fields. That is not a coincidence; it is the exercise.

---

## Bash Script: Line Count Difference

Create a script that accepts the 2 files as arguments and prints the difference in the line counts. This is the whole job, in miniature: two numbers, a check that you got two arguments, and no AI-generated CSV parser required. Put it in `scratch/` if you like; that folder is gitignored on purpose. A worked answer lives in [`solutions/linecount`](../solutions/linecount).

### Steps

1. Store the count of 1st file in a variable
2. Store count of 2nd file in a variable
3. Print the difference

- The script should error out if arguments are not 2.
- Call the script `linecount`.

Run it on the pair from this chapter:

```sh
./linecount banklist.csv banklist-scrubbed.csv   # 41
```

If yours says `41`, you can count lines, do arithmetic, and check arguments. That is most of scripting.

---

## More Resources

- [Linux Journey](https://linuxjourney.com/) – More Linux learning resources
- [Digital Ocean Linux](https://www.digitalocean.com/community/tags/linux-basics)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [sed One-Liners Explained](https://catonmat.net/sed-one-liners-explained-part-one)

---

[Next: AWK Tutorial →](tools_awk.md)
