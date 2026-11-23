# User and Group Management in Linux

UID 0 is root. This chapter is `/etc/passwd`, `/etc/group`, and the commands that add people to a machine.

**About 30 minutes. Open this when you are adding a user.** Done when you can explain `useradd -m` and the `-a` in `usermod -aG`.

Setting up Mint from [linux-setup](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#57-users-and-groups)? This is the chapter that setup uses. It is not part of the two-hour shell path.

[← Two-hour path](../README.md#if-you-are-new)

---

## 1. Key System Files

These three files are the phone book. They are ordinary text. `less` them before you edit them.

- `/etc/passwd` — User accounts: username, UID, GID, home directory, shell. Despite the name, the passwords do not live here anymore. That would have been too exciting.
- `/etc/group` — Groups: name, GID, members.
- `/etc/shadow` — Password hashes and aging. Root can read it; you usually cannot, which is the point.

Fields are colon-separated. `cut -d:` is how you pick a column without inventing a parser.

---

## 2. Viewing Users and Groups

Look first. Adding a user is easy; noticing you already have one named `newuser` is easier if you look.

- List all users:

  ```sh
  cut -d: -f1 /etc/passwd
  ```

- List all groups:

  ```sh
  cut -d: -f1 /etc/group
  ```

- Show your current user (for when you have lost the plot):

  ```sh
  whoami
  ```

- Show groups you belong to:

  ```sh
  groups
  ```

- Show details for a specific user — UID, GID, and every group. This is the useful one:

  ```sh
  id username
  ```

`id` with no name is you. Try it.

---

## 3. Adding and Managing Users

Practice on a throwaway account or a VM, not the login you are using. `useradd` without `-m` creates a user with no home directory, which is a sad way to exist. Use `-m` unless you meant a system account.

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

`userdel` leaves the home directory behind unless you pass `-r`. That is a kindness when you meant to keep the files, and a surprise disk-full when you did not.

---

## 4. Adding and Managing Groups

A group is a nametag you put on people so they can share files. `sudo` is a group on many distros. That is why the next flag matters.

- Add a new group:

  ```sh
  sudo groupadd newgroup
  ```

- Delete a group:

  ```sh
  sudo groupdel newgroup
  ```

- Add a user to a group. The `-a` is "append." Without it, `usermod -G` *replaces* their groups with just this one — a surprisingly effective way to drop someone from `sudo`:

  ```sh
  sudo usermod -aG groupname username
  ```

- Remove a user from a group (edit `/etc/group` manually or use `gpasswd`):

  ```sh
  sudo gpasswd -d username groupname
  ```

---

## 5. Useful Tips

- Group membership often waits until the next login. A new terminal (or `newgrp`) is faster than rebooting out of superstition.
- Read `/etc/passwd` and `/etc/group` with `cat`, `less`, or `grep`. They are text. Treat them that way.
- System users (for services like `www-data`) usually have low UIDs. The exact cutoff depends on the distro, so do not memorize a number — look at the file.

---

## References

- [Linux User Management Guide](https://www.cyberciti.biz/faq/howto-add-remove-user-account/)
- [man useradd](https://man7.org/linux/man-pages/man8/useradd.8.html)
- [man groupadd](https://man7.org/linux/man-pages/man8/groupadd.8.html)
- [man usermod](https://man7.org/linux/man-pages/man8/usermod.8.html)

[Next: Build Systems: Make and Ninja →](build_systems.md)
