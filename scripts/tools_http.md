# HTTP Tools: wget & curl Basics

The browser is a fine client until you need a header, a POST body, or a file on a headless box. `wget` downloads. `curl` is a Swiss Army knife that also happens to download, which is why people argue about them on the internet instead of at work. `scp` and `sftp` move files over SSH.

> **In the age of AI:** Agents are fluent in `curl` and slightly too fluent in `curl | sh`. Read the URL. Look at `-H "Authorization: ..."`. Do not commit the token the model helpfully inlined, and do not pipe an installer you have not opened. `--fail` is how a script notices the server said 404 instead of treating an error page as a successful download.

[← Back: AWK Tutorial](tools_awk.md) | [Next: User and Group Management →](users_groups.md)

No HTTP assignment yet? The workstation chapter's REST-client section links back here when you need it. [Users and groups](users_groups.md) is the page the Mint setup uses next.

---

## wget Basics

`wget` is the download button. It retries, it resumes, it writes a file and gets out of the way. Good at "get this and wait," and at recursive fetches if you really meant a mirror.

### Download a Single File

```sh
wget https://example.com/file.txt
```

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
