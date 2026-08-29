# AWK Tutorial: Analyzing nginx.log

AWK is the "I have a log file and twenty minutes" language. It is not pretty. Logs do not care about pretty. It reads line by line, splits into fields, and lets you count, filter, and summarize without opening Excel or writing Python.

The file `nginx.log` is in this folder. Print a line first. Count the fields. Then write the one-liner.

> **In the age of AI:** Models guess `$9` because they have seen Combined Log Format. They also guess wrong when the log is slightly different, quoted, or behind a proxy that stuffed an extra field in. Ask the agent for a draft; `awk '{print $9}'` on *this* file is the review. Incident work is the same loop: narrow with AWK, paste the summary into the chat, not the entire access log.

[← Back: sed, sort, uniq](tools_sed.md) | [Next: HTTP Tools →](tools_http.md)

---

## 1. Print the Whole Log

```sh
awk '{print}' nginx.log
```
This simply prints every line (like `cat`).

---

## 2. Print Only the IP Addresses

The IP address is the **first field** in each line.

```sh
awk '{print $1}' nginx.log
```

---

## 3. Count Unique IP Addresses

```sh
awk '{print $1}' nginx.log | sort | uniq | wc -l
```
- `awk '{print $1}'` prints the IP.
- `sort | uniq` finds unique IPs.
- `wc -l` counts them.

---

## 4. Print Only the HTTP Status Codes

The status code is the **9th field**.

```sh
awk '{print $9}' nginx.log
```

---

## 5. Count Requests by Status Code

```sh
awk '{print $9}' nginx.log | sort | uniq -c | sort -nr
```
This shows how many times each status code appears.

---

## 6. Show Only 404 Errors

```sh
awk '$9 == 404' nginx.log
```
Prints lines where the status code is 404.

---

## 7. Count 404 Errors

```sh
awk '$9 == 404' nginx.log | wc -l
```

---

## 8. Print Requested URLs

The URL is the **7th field** (inside the quotes, so you may need to tweak the field separator):

```sh
awk '{print $7}' nginx.log
```
But in your log, the request is in quotes, so use a custom field separator:

```sh
awk -F\" '{print $2}' nginx.log
```
This prints the HTTP request (e.g., `GET /downloads/product_1 HTTP/1.1`).

To get just the path:

```sh
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log
```

---

## 9. Top Requested URLs

```sh
awk -F\" '{split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr | head
```

---

## 10. Combine Filters: 404s by URL

```sh
awk '$9 == 404 {print $7}' nginx.log | sort | uniq -c | sort -nr
```
Or, for more accurate URL extraction:

```sh
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print a[2]}' nginx.log | sort | uniq -c | sort -nr
```

---

## 11. Print IP and URL for 404s

```sh
awk -F\" '$0 ~ / 404 / {split($2, a, " "); print $1, a[2]}' nginx.log
```

---

## 12. AWK One-Liner to Summarize All Status Codes

```sh
awk '{count[$9]++} END {for (code in count) print code, count[code]}' nginx.log | sort -k2,2nr
```

---

## 13. AWK Built-in Functions

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

---

# References

- [GNU AWK Manual](https://www.gnu.org/software/gawk/manual/gawk.html)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [Digital Ocean AWK Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-the-awk-language-to-manipulate-text-in-linux)
- [NGINX Access Logs](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs)

---


[Next: HTTP Tools: wget & curl →](tools_http.md)
