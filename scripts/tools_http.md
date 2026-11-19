# HTTP Tools: wget & curl Basics

`wget` downloads. `curl` downloads and also speaks HTTP. `scp` and `sftp` move files over SSH.

**About 30 minutes. Open this when you have a URL.** Done when you have downloaded one file and read one response header.

[← Two-hour path](../README.md#if-you-are-new) | [Users and groups, when you need them](users_groups.md)

---

## wget Basics

`wget` is the download button. It retries, it resumes, it writes a file and gets out of the way. Good at "get this and wait," and at recursive fetches if you really meant a mirror.

### Download a Single File

```sh
wget https://example.com/file.txt
```

Read the URL before you run it. A token in `-H "Authorization: ..."` does not belong in a repo, and `curl | sh` is an installer you have not opened yet.

The filename is whatever the URL's last component is. Sometimes that is `file.txt`. Sometimes it is `download.php?id=12` and your directory looks haunted.

### Download and Save with a Custom Name

```sh
wget -O myfile.txt https://example.com/file.txt
```

`-O` is "save as." Use it when you care what the file is called.

### Download in the Background

```sh
wget -b https://example.com/largefile.zip
```

Go make tea. The log is `wget-log` in the current directory.

### Download an Entire Website (recursive)

This will fetch a lot of files. Confirm you meant a mirror, and that the remote host is fine with it.

```sh
wget --recursive --no-parent https://example.com/
```

`--no-parent` is "stay in this folder, do not wander up the site." Without it, `wget` is an enthusiastic tourist.

---

## curl Basics

`curl` talks HTTP (and a dozen other protocols) and prints the body to **stdout**. That is why it shows up in every "how do I hit this API" answer, and why a forgotten `-o` dumps a zip file into your terminal.

### Download a File

```sh
curl --fail --location --remote-name https://example.com/file.txt
```

`--location` follows redirects. `--remote-name` (`-O`) keeps the remote filename. `--fail` makes a 404 an actual error instead of a successful download of an error page.

### Download and Save with a Custom Name

```sh
curl --fail --location --output myfile.txt https://example.com/file.txt
```

### Fetch HTTP Headers Only

```sh
curl -I https://example.com
```

Ask for the headers, skip the body. Useful when you only care "is it 200?" or "what is `Content-Type`?"

### Make a POST Request

```sh
curl --fail --location --data 'name=amit&age=30' https://example.com/api
```

`--data` is a form post. The browser does this when you hit Submit; `curl` does it when you are not a browser.

### Send Custom Headers

```sh
curl --fail --header "Authorization: Bearer TOKEN" https://example.com/protected
```

`TOKEN` is a placeholder. A real token does not belong in the tutorial, the shell history, or the repo.

For scripts, add a timeout such as `--connect-timeout 10 --max-time 60` so a hung server is not a hung terminal. `curl` does not treat an HTTP 4xx or 5xx as a command failure unless you use `--fail` or `--fail-with-body`.

---

## Reading JSON Responses with jq

Most APIs answer `curl` with a single-line wall of JSON. `jq` is `sed` and `awk` for structured JSON — pipe `curl`'s stdout into it to pretty-print, filter fields, and reshape responses without writing a script.

- [jq Home](https://jqlang.org/) — overview and installation
- [jq Tutorial](https://jqlang.org/tutorial/) — quick hands-on walkthrough with real API responses
- [jq Manual](https://jqlang.org/manual/) — full filter, operator, and built-in function reference
- [jq Play](https://jqplay.org/) — interactive browser playground for testing filters
- [jq on GitHub](https://github.com/jqlang/jq) — source repository and releases

---

## scp and sftp: Secure File Transfer

Same job as `wget`/`curl`, but over SSH, to a machine you already have a login on. If you can `ssh` there, you can copy files there.

### scp (Secure Copy)

`scp` is `cp` with a `user@host:` on one side. Mental model stays the same.

- **Copy a file to a remote server:**

  ```sh
  scp localfile.txt username@remote_host:/path/to/destination/
  ```

- **Copy a file from a remote server:**

  ```sh
  scp username@remote_host:/path/to/file.txt ./
  ```

- **Copy a directory recursively:**

  ```sh
  scp -r myfolder username@remote_host:/path/to/destination/
  ```

The colon is load-bearing. Forget it and `scp` thinks you meant a local file named `username@remote_host`.

### sftp (SSH File Transfer Protocol)

`sftp` is an interactive session: `ls`, `cd`, `get`, `put`. FTP's more trustworthy cousin. `exit` is how you leave.

- **Start an SFTP session:**

  ```sh
  sftp username@remote_host
  ```

- **Common sftp commands:**
  - `ls` — List files on the remote server
  - `cd` — Change directory on the remote server
  - `get file.txt` — Download a file
  - `put file.txt` — Upload a file
  - `exit` — Quit sftp

---

## References

- [Wget Manual](https://www.gnu.org/software/wget/manual/wget.html)
- [Curl Manual](https://curl.se/docs/manual.html)
- [DigitalOcean: How To Use Wget](https://www.digitalocean.com/community/tutorials/how-to-use-wget-to-download-files-and-interact-with-rest-apis)
- [DigitalOcean: How To Use Curl](https://www.digitalocean.com/community/tutorials/how-to-use-curl-to-transfer-data)
- [DigitalOcean: How To Use SFTP](https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files)
- [DigitalOcean: How To Use SCP](https://www.digitalocean.com/community/tutorials/how-to-use-scp-to-securely-transfer-files)

[Next: User and Group Management →](users_groups.md)
