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

This prints every line, the way `cat` would. `$9` is the status code on *this* file. Confirm with `head -1` before you trust a field number from a chat window.

---

## 2. Print Only the IP Addresses

By default, fields are whitespace-separated. `$1` is the left edge. On a Combined Log Format line, that is the client IP.

```sh
awk '{print $1}' nginx.log
```

Print one raw line with `head -1 nginx.log` and count the fields if your log looks different. AWK is not psychic; it is just numbered.

---

## 3. Count Unique IP Addresses

```sh
awk '{print $1}' nginx.log | sort | uniq | wc -l
```
- `awk '{print $1}'` prints the IP.
- `sort | uniq` finds unique IPs.
- `wc -l` counts them.

`uniq` only collapses *adjacent* duplicates. Without `sort` first, it will lie to you with a straight face. Same joke as in the sed chapter; it is worth repeating.

---

## 4. Print Only the HTTP Status Codes

The status code is the **9th field** — on *this* log. That is a Combined Log Format habit, not a law of nature.

```sh
awk '{print $9}' nginx.log
```

If you see URLs or dashes where you expected `200`, you counted wrong. Go back to section 2.

---

## 5. Count Requests by Status Code

```sh
awk '{print $9}' nginx.log | sort | uniq -c | sort -nr
```
`uniq -c` counts; `sort -nr` puts the popular codes on top. This is the classic "how was today?" histogram.

---

## 6. Show Only 404 Errors

A pattern before the block is a filter. No `{print}` needed: AWK prints the whole line by default.

```sh
awk '$9 == 404' nginx.log
```
Prints lines where the status code is 404.

---

## 7. Count 404 Errors

```sh
awk '$9 == 404' nginx.log | wc -l
```

Same filter, then count. If the number is huge, you have a missing page. If it is zero, either the site is fine or `$9` is not the status.

---

## 8. Print Requested URLs

The URL is the **7th field** (inside the quotes, so you may need to tweak the field separator):

```sh
awk '{print $7}' nginx.log
```
Quotes in the log are why `$7` is a polite lie. Cut on `"` instead:

```sh
awk -F\" '{print $2}' nginx.log
```
This prints the HTTP request (e.g., `GET /downloads/product_1 HTTP/1.1`).

To get just the path:

```sh
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log
```

Ugly? Yes. Faster than opening Python to split one quoted string? Also yes.

---

## 9. Top Requested URLs

```sh
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr | head
```

Same histogram as the status codes, now for paths. `head` keeps you from listing the entire internet.

---

## 10. Combine Filters: 404s by URL

Which missing pages? First form is quick; the quote-splitting one is fussier and more accurate.

```sh
awk '$9 == 404 {print $7}' nginx.log | sort | uniq -c | sort -nr
```
Or, for more accurate URL extraction:

```sh
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr
```

---

## 11. Print IP and URL for 404s

Who asked for the missing page?

```sh
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print $1, a[2]}' nginx.log
```

---

## 12. AWK One-Liner to Summarize All Status Codes

You can skip the `sort | uniq -c` pipeline and let AWK count. `END` runs once, after the last line — the closing credits.

```sh
awk '{count[$9]++} END {for (code in count) print code, count[code]}' nginx.log | sort -k2,2nr
```

---

## 13. AWK Built-in Functions

The "I refuse to open Python for this" toolkit. You do not need all of them. You need to know they exist.

### String Functions

- **substr(string, start, length)** - Extract substring:

  ```sh
  # Extract first 3 characters of IP address
  awk '{print substr($1, 1, 3)}' nginx.log
  ```

- **length(string)** - Get string length:

  ```sh
  # Print lines where URL is longer than 20 characters
  awk -F\" 'length($2) > 20 {print $2}' nginx.log
  ```

- **gsub(regex, replacement, string)** - Global substitution:

  ```sh
  # Replace all dots in IP addresses with dashes
  awk '{gsub(/\./, "-", $1); print $1}' nginx.log
  ```

- **toupper(string)** and **tolower(string)** - Change case:

  ```sh
  # Convert HTTP method to uppercase
  awk -F\" '{split($2, a, " "); print toupper(a[1])}' nginx.log
  ```

### Numeric Functions

- **int(number)** - Convert to integer:

  ```sh
  # Round down response sizes
  awk '{print int($10)}' nginx.log
  ```

### Date/Time Functions

- **strftime(format, timestamp)** - Format timestamps:

  ```sh
  # Nginx $4 is a formatted string, not a Unix timestamp. Parse it first
  # with gawk's mktime(), or use the original timestamp as text.
  awk -F'[][]' '{gsub("/", " ", $2); print $2}' nginx.log
  ```

This file writes `[17/May/2015:08:05:32 +0000]`, not a Unix epoch. `strftime` wants a number. The snippet above peels the brackets off and leaves the date as text, which is usually what you wanted anyway.

---

# References

- [GNU AWK Manual](https://www.gnu.org/software/gawk/manual/gawk.html)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [Digital Ocean AWK Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-the-awk-language-to-manipulate-text-in-linux)
- [NGINX Access Logs](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs)

---


[Next: HTTP Tools: wget & curl →](tools_http.md)
