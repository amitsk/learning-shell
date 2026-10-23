# Solutions

Answers for the exercises that ask for a number or a script. Try first. The point is the pipeline, not the file.

| Exercise | How | Expected |
| --- | --- | --- |
| `linecount` | [`linecount`](linecount) with the two files | `41` |
| Distinct column 3, raw CSV | `cut -d , -f3 banklist.csv`, then `sort -f`, then `uniq -c` | `81` values, many not states |
| Distinct states, scrubbed CSV | same pipeline on `banklist-scrubbed.csv` | `45` lines including the header, so 44 states |
| Arizona banks, raw | `grep -c ',AZ,' banklist.csv` | `16` |
| Arizona banks, scrubbed | `grep -c ',AZ,' banklist-scrubbed.csv` | `11` |
| Banks with "savings" in the name | `grep -ic savings banklist-scrubbed.csv` | `34` |
| CRLF lines | `grep -c $'\r$' banklist.csv` | `572` |

```sh
cd scripts
sed -n '/^"/!p' < banklist.csv > /tmp/banklist-scrubbed.csv
chmod +x ../solutions/linecount
../solutions/linecount banklist.csv /tmp/banklist-scrubbed.csv
```

If your number is off by one, the header got counted. If it is off by five, you scrubbed the quoted rows and kept counting anyway.
