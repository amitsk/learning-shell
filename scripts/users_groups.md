# User and Group Management in Linux

[End of Flow]

This guide covers the basics of user and group management, including important system files and common commands.

---

## 1. Key System Files

- `/etc/passwd` — Stores user account information (username, UID, GID, home directory, shell, etc.)
- `/etc/group` — Stores group information (group name, GID, group members)
- `/etc/shadow` — Stores encrypted user passwords (usually only accessible by root)

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
- System users (for services) usually have UIDs below 1000.

---

## References

- [Linux User Management Guide](https://www.cyberciti.biz/faq/howto-add-remove-user-account/)
- [man useradd](https://man7.org/linux/man-pages/man8/useradd.8.html)
- [man groupadd](https://man7.org/linux/man-pages/man8/groupadd.8.html)
- [man usermod](https://man7.org/linux/man-pages/man8/usermod.8.html)

[Next: Build Systems: Make and Ninja →](build_systems.md)
