# AWK Tutorial: Analyzing nginx.log

---

## What is AWK?

AWK is a powerful text-processing language ideal for working with columnar data, such as logs and CSV files. It processes input line by line, splitting each line into fields (by default, whitespace).

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
awk '{count[$9]++} END {for (code in count) print code, count[code]}' nginx.log | sort -nr
```

---

# References

- [GNU AWK Manual](https://www.gnu.org/software/gawk/manual/gawk.html)
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [Digital Ocean AWK Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-the-awk-language-to-manipulate-text-in-linux)
- [NGINX Access Logs](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs)

---


[Next: HTTP Tools: wget & curl →](tools_http.md)