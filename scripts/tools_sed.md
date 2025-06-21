# Linux Shell Practice: sed, sort, uniq, and More

[All Linux Cheatsheets](http://www.nixtutor.com/linux/all-the-best-linux-cheat-sheets/)

---

## Commands to Practice

- `pwd`, `cd`, `chmod`, `chown`, `ls` (with flags), `rm`, `mkdir`, `tail`, `head`, `<`, `grep`, `|`

---

## Download files with `wget` and `curl`
```
wget  https://www.digitalocean.com/robots.txt
```

### Check Download and File Attributes

1. Use the `ls` command to check if download worked.
2. Look at file attributes:

  ```sh
  ls -lrt
  ```

3. Check file ownership

#### More  References
* [Wget Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-wget-to-download-files-and-interact-with-rest-apis)
* [Curl](https://www.digitalocean.com/community/tutorials/workflow-downloading-files-curl) 



---

## Working with `banklist.csv`

Refer to `banklist.csv` in the same folder as this file.

## Viewing File Contents

- **Print first 5 lines:**

  ```sh
  head -5 < banklist.csv
  cat banklist.csv | head -5
  ```

- **Print last 5 lines:**

  ```sh
  cat banklist.csv | tail -5
  ```

- **Print the 2nd last line:**

  ```sh
  tail -2 banklist.csv | head -1
  ```
- **Print specific columns using cut:**

  ```sh
  cat banklist.csv | cut -d , -f 1
  # Print the first column of a comma delimited file
  ```

---

## Removing Lines with Leading Quotes Using `sed`

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

- **Remove all lines with leading quotes (`^"`):**

  ```sh
  sed -n '/^"/!p' < banklist.csv > banklist-scrubbed.csv
  ```

---

## Sorting Bank Names

- **Sort ascending and descending using the `sort` command.**
- **Options:**
  - Try ascending and then descending
  - Try ignoring lowercase/uppercase

---

## Other Useful Commands

- **Print the last 5 and the first 5 lines:**

  ```sh
  head -5 banklist.csv
  tail -5 banklist.csv
  ```

- **Count the number of banks:**

  ```sh
  wc -l banklist.csv
  ```

- **Remove duplicates and count:**

  ```sh
  uniq banklist-scrubbed.csv | wc -l
  wc -l banklist-scrubbed.csv
  ```

- **Why are counts the same?**

---

## States List and Sorting

- **Print out the list of all States (unique, sorted):**

  ```sh
  cut -d , -f3 banklist-scrubbed.csv | sort -f | uniq
  ```

---

## Filtering and Counting by State

- **Get the list of all banks in AZ:**

  ```sh
  grep ',AZ,' banklist-scrubbed.csv
  ```

- **Count of all banks in AZ:**

  ```sh
  grep ',AZ,' banklist-scrubbed.csv | wc -l
  ```

---

## Comparing File Line Counts

- **Compare the count in the 2 files:**

  ```sh
  wc -l banklist.csv
  wc -l banklist-scrubbed.csv
  ```

---

## Bash Script: Line Count Difference

Create a script that accepts the 2 files as arguments and prints the difference in the line counts.

### Steps:

1. Store the count of 1st file in a variable
2. Store count of 2nd file in a variable
3. Add them using an expression

- The script should error out if arguments are not 2.
- Call the script `linecount`.

---

[Linux Journey](https://linuxjourney.com/) – More Linux learning resources
[Digital Ocean Linux](https://www.digitalocean.com/community/tags/linux-basics)