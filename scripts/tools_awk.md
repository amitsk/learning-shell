# AWK Tutorial: Analyzing nginx.log

AWK reads a log line by line, splits it into fields, and lets you count. The file `nginx.log` is in this folder.

**About 30 minutes. Open this when you have a log.** Done when you can print one field and a count from `nginx.log`.

No log in the assignment yet? Come back later. The sample file next to this page is enough for the half hour.

[← Two-hour path](../README.md#if-you-are-new) | [sed, if you have a CSV](tools_sed.md)

---

## 1. Print the Whole Log

Hello, world. This is `cat` with extra steps, and it proves the file opens.

```sh
awk '{print}' nginx.log
```

---

## 2. Print and Count Unique IP Addresses

By default, AWK splits on whitespace (`$1` is the leftmost field — on a Combined Log Format line, the client IP). Check one raw line with `head -1 nginx.log` if your log looks different; AWK is numbered, not psychic.

```sh
awk '{print $1}' nginx.log                       # all IPs
awk '{print $1}' nginx.log | sort | uniq | wc -l # 24 unique IPs
```

`uniq` only collapses *adjacent* duplicates, so `sort` always goes first.

---

## 3. HTTP Status Codes and Histograms

On *this* log, the status code is the **9th whitespace-separated field** (`$9`). That is a property of this file, not a law of nature — if you see URLs or dashes instead of `200`, inspect `head -1 nginx.log` and recount.

```sh
awk '{print $9}' nginx.log                               # status codes
awk '{print $9}' nginx.log | sort | uniq -c | sort -nr   # histogram
```

On this log: **304 × 60**, **404 × 29**, **200 × 11**. A hundred requests, mostly browsers asking "has this changed?" — a quiet site, not a broken one.

---

## 4. Show and Count 404 Errors

A condition before the block filters lines (omitting `{print}` prints the whole matching line by default):

```sh
awk '$9 == 404' nginx.log          # show 404 lines
awk '$9 == 404' nginx.log | wc -l  # 29 missing-page requests
```

If the count is zero on a site you know is throwing 404s, `$9` is probably not the status column.

---

## 5. Extract and Rank Requested URLs

Whitespace splitting makes `$7` the path only as long as the request string has no spaces inside it. Splitting on quotes (`-F\"`) is safer:

```sh
awk -F\" '{print $2}' nginx.log                                                       # full request line
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log                                  # path only
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr | head  # top URLs
```

Ugly? Yes. Faster than opening Python to split one quoted string? Also yes.

---

## 6. Combine Filters: 404s by URL and IP

Which missing pages, and who asked for them?

```sh
# 404s by URL (21 for /downloads/product_1, 8 for /downloads/product_2)
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr

# Client IP + requested path for each 404
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print $1, a[2]}' nginx.log
```

---

## 7. Summarize Without `sort | uniq -c`

AWK has associative arrays. `END` runs once after the last line — the closing credits:

```sh
awk '{count[$9]++} END {for (code in count) print code, count[code]}' nginx.log | sort -k2,2nr
```

---

## 8. AWK Built-in Functions

The "I refuse to open Python for this" toolkit (full list in the [GNU AWK Manual](https://www.gnu.org/software/gawk/manual/gawk.html#Built_002din)):

- **Strings:** `substr($1, 1, 3)` (slice), `length($2)` (length), `gsub(/\./, "-", $1)` (global replace), `toupper(...)` / `tolower(...)`:

  ```sh
  awk '{gsub(/\./, "-", $1); print $1}' nginx.log
  awk -F\" 'length($2) > 20 {print $2}' nginx.log
  ```

- **Numbers:** `int($10)` rounds down response sizes:

  ```sh
  awk '{print int($10)}' nginx.log
  ```

- **Timestamps:** GNU AWK's `strftime("%Y-%m-%d", systime())` formats Unix epochs, but `nginx.log` writes text like `[17/May/2015:08:05:32 +0000]`. Usually peeling the brackets off is all you actually want:

  ```sh
  awk -F'[][]' '{gsub("/", " ", $2); print $2}' nginx.log
  ```

---

## References

- [GNU AWK Manual](https://www.gnu.org/software/gawk/manual/gawk.html)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [Digital Ocean AWK Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-the-awk-language-to-manipulate-text-in-linux)
- [NGINX Access Logs](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs)

---


[Next: HTTP Tools: wget & curl →](tools_http.md)
