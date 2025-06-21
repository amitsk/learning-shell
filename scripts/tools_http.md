# HTTP Tools: wget & curl Basics

This guide introduces the basics of using `wget` and `curl` for downloading files and interacting with web servers from the command line.

---

## wget Basics

`wget` is a command-line utility for downloading files from the web.

### Download a Single File

```sh
wget https://example.com/file.txt
```

### Download and Save with a Custom Name

```sh
wget -O myfile.txt https://example.com/file.txt
```

### Download in the Background

```sh
wget -b https://example.com/largefile.zip
```

### Download an Entire Website (recursive)

```sh
wget --recursive --no-parent https://example.com/
```

---

## curl Basics

`curl` is a versatile tool for transferring data to or from a server, supporting many protocols (HTTP, FTP, etc.).

### Download a File

```sh
curl -O https://example.com/file.txt
```

### Download and Save with a Custom Name

```sh
curl -o myfile.txt https://example.com/file.txt
```

### Fetch HTTP Headers Only

```sh
curl -I https://example.com
```

### Make a POST Request

```sh
curl -X POST -d "name=amit&age=30" https://example.com/api
```

### Send Custom Headers

```sh
curl -H "Authorization: Bearer TOKEN" https://example.com/protected
```

---

## scp and sftp: Secure File Transfer

### scp (Secure Copy)

`scp` lets you securely copy files between your local machine and a remote server over SSH.

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

### sftp (SSH File Transfer Protocol)

`sftp` provides an interactive session for transferring files over SSH.

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