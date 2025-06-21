# HTTP Tools: wget & curl Basics

[Next: User and Group Management →](users_groups.md)

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

## References

- [Wget Manual](https://www.gnu.org/software/wget/manual/wget.html)
- [Curl Manual](https://curl.se/docs/manual.html)
- [DigitalOcean: How To Use Wget](https://www.digitalocean.com/community/tutorials/how-to-use-wget-to-download-files-and-interact-with-rest-apis)
- [DigitalOcean: How To Use Curl](https://www.digitalocean.com/community/tutorials/how-to-use-curl-to-transfer-data)
