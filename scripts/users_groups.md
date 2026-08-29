# User and Group Management in Linux

UID 0 is root. Treat it like a live wire. This chapter is `/etc/passwd`, `/etc/group`, and the commands that add or remove people from a machine — which is ordinary on a laptop and a change-control event on a server.

> **In the age of AI:** A model will happily draft `userdel`, `usermod -aG sudo`, or a `chown -R`. Read it. `useradd` without `-m` is a user with no home. `usermod -aG` without the `-a` is a user whose other groups you just erased. Do not paste account changes onto a box you like until you can explain the flags.

[← Back: HTTP Tools](tools_http.md) | [Next: Build Systems →](build_systems.md)

---

## 1. Key System Files

- `/etc/passwd` — Stores user account information (username, UID, GID, home directory, shell, etc.)
- `/etc/group` — Stores group information (group name, GID, group members)
- `/etc/shadow` — Stores password hashes and related aging information (usually only accessible by root)

---

## 2. Viewing Users and Groups

- List all users:

  ```sh
  cut -d: -f1 /etc/passwd
  ```

- List all groups:

  ```sh
  cut -d: -f1 /etc/group
  ```

- Show your current user:

  ```sh
  whoami
  ```

- Show groups you belong to:

  ```sh
  groups
  ```

- Show details for a specific user:

  ```sh
  id username
  ```

---

## 3. Adding and Managing Users

- Add a new user:

  ```sh
  sudo useradd newuser
  sudo passwd newuser
  ```

- Add a user with a home directory:

  ```sh
  sudo useradd -m newuser
  ```

- Delete a user:

  ```sh
  sudo userdel newuser
  ```

---

## 4. Adding and Managing Groups

- Add a new group:

  ```sh
  sudo groupadd newgroup
  ```

- Delete a group:

  ```sh
  sudo groupdel newgroup
  ```

- Add a user to a group:

  ```sh
  sudo usermod -aG groupname username
  ```

- Remove a user from a group (edit `/etc/group` manually or use `gpasswd`):

  ```sh
  sudo gpasswd -d username groupname
  ```

---

## 5. Useful Tips

- Changes to group membership may require the user to log out and back in to take effect.
- You can view the contents of `/etc/passwd` and `/etc/group` with `cat`, `less`, or `grep`.
- System users (for services) often have low UIDs, but the exact threshold is distribution- and configuration-dependent.

---

## References

- [Linux User Management Guide](https://www.cyberciti.biz/faq/howto-add-remove-user-account/)
- [man useradd](https://man7.org/linux/man-pages/man8/useradd.8.html)
- [man groupadd](https://man7.org/linux/man-pages/man8/groupadd.8.html)
- [man usermod](https://man7.org/linux/man-pages/man8/usermod.8.html)

[Next: Build Systems: Make and Ninja →](build_systems.md)
