---
date : '2025-07-18T22:14:26+02:00'
draft : false
title : 'Linux Users and Groups'
summary : 'A technical reference for understanding and managing Linux user groups and file permissions, including commands, best practices, and security tips.'
tags : ['Linux', 'System Administration', 'Permissions']
categories : ['Linux', 'System Administration']
---


---

# Linux User Groups & File Permissions

This guide serves as a structured, technical overview of **Linux user groups**, **permissions**, and related command-line utilities. It is organized for memory recall and hands-on administration.

---

## 📁 1. User Groups Basics

| Concept              | Description                                                            |
| -------------------- | ---------------------------------------------------------------------- |
| **User**             | A system identity with a UID, used to log in and execute commands      |
| **Group**            | A collection of users identified by a GID, used for shared permissions |
| **Primary Group**    | The default group associated with a user (shown in `/etc/passwd`)      |
| **Secondary Groups** | Additional groups a user belongs to (shown in `/etc/group`)            |

### 🔹 Built-in vs Custom Groups

| Type         | Description                    | Examples                 |
| ------------ | ------------------------------ | ------------------------ |
| **Built-in** | Created during OS installation | `root`, `wheel`, `users` |
| **Custom**   | Manually created by sysadmins  | `devops`, `qa_team`      |

---

## 👥 2. Working with User Groups

### Creating and Managing Groups

| Operation              | Command                                     |
| ---------------------- | ------------------------------------------- |
| Create a new group     | `sudo groupadd devteam`                     |
| Delete a group         | `sudo groupdel devteam`                     |
| Rename a group         | `sudo groupmod -n newname oldname`          |
| Add user to group      | `sudo usermod -aG devteam alice`            |
| View user groups       | `groups alice` or `id alice`                |
| Change primary group   | `sudo usermod -g devteam alice`             |
| Remove user from group | Edit `/etc/group` manually or use `gpasswd` |

---

## 🔐 3. How File Permissions Work

| Type        | Symbol | Octal | Description                                |
| ----------- | ------ | ----- | ------------------------------------------ |
| **Read**    | `r`    | 4     | View contents of file/directory            |
| **Write**   | `w`    | 2     | Modify file or directory contents          |
| **Execute** | `x`    | 1     | Run file as a program or enter a directory |

### Permission Structure

```
-rwxr-xr--
| |  |  |
| |  |  └─ Others: read (r), no write (-), no execute (-)
| |  └──── Group: read (r), no write (-), execute (x)
| └─────── Owner: read (r), write (w), execute (x)
```

| Octal | Symbolic | Description    |
| ----- | -------- | -------------- |
| `7`   | `rwx`    | Full access    |
| `6`   | `rw-`    | Read + Write   |
| `5`   | `r-x`    | Read + Execute |
| `4`   | `r--`    | Read only      |
| `0`   | `---`    | No access      |

---

## 📂 4. Folder vs File Permissions

| Permission    | Files Meaning                   | Directories Meaning                     |
| ------------- | ------------------------------- | --------------------------------------- |
| `r` (read)    | View contents                   | List directory files                    |
| `w` (write)   | Modify file                     | Create/delete/rename files in directory |
| `x` (execute) | Run the file (if binary/script) | Access into directory (`cd`)            |

---

## ⚙️ 5. Key Group and Permission Commands

### File Permission Tools

| Command   | Purpose                                   | Example                          |
| --------- | ----------------------------------------- | -------------------------------- |
| `chmod`   | Change file permissions                   | `chmod 755 script.sh`            |
| `chown`   | Change file owner and/or group            | `chown alice:devteam file.txt`   |
| `chgrp`   | Change only group ownership               | `chgrp devteam file.txt`         |
| `umask`   | Set default permission mask for new files | `umask 022`                      |
| `setfacl` | Set Access Control List (ACL)             | `setfacl -m g:qa:r-- report.txt` |
| `getfacl` | View ACLs                                 | `getfacl report.txt`             |

---

## 🚨 6. Special Permission Types

| Special Bit    | Symbol | Description                                                            |
| -------------- | ------ | ---------------------------------------------------------------------- |
| **Setuid**     | `s`    | Executable runs with owner's privileges                                |
| **Setgid**     | `s`    | Files: runs with group privileges; Dirs: files inherit group ownership |
| **Sticky Bit** | `t`    | On directories, only owner can delete or rename files                  |

```bash
chmod u+s file    # Setuid
chmod g+s dir     # Setgid
chmod +t /tmp     # Sticky Bit
```

---

## 🛡️ 7. Security Tips and Good Habits

### 🔐 Minimum Access Rules

| Practice                         | Justification                                    |
| -------------------------------- | ------------------------------------------------ |
| Principle of least privilege     | Reduce risk by limiting access                   |
| Avoid world-writable permissions | `chmod o-w file` to remove write from others     |
| Use groups for shared access     | Controlled collaboration via `chmod` and `chgrp` |

### 🛠️ Planning Group Permissions

1. Create specific groups for each role/project.
2. Set group ownership on relevant files.
3. Adjust directory permissions using `chmod 770 dir` for full group access.
4. Use **ACLs** for finer-grained controls beyond owner/group/others.

### 📜 Using ACLs

| Command                       | Purpose                           |
| ----------------------------- | --------------------------------- |
| `setfacl -m u:alice:r-- file` | Grant read-only access to `alice` |
| `setfacl -x u:alice file`     | Remove `alice` from ACL           |
| `getfacl file`                | View current ACLs                 |

---

## 🧭 8. Permission Inheritance and Default Settings

### Default File Creation: `umask`

| `umask` Value | Default Permission for File | Explanation |
| ------------- | --------------------------- | ----------- |
| `022`         | `644`                       | rw-r--r--   |
| `027`         | `640`                       | rw-r-----   |

### ACL Inheritance for Directories

```bash
# Set default ACL on directory
setfacl -d -m g:devteam:rwX project_dir
```

---

## 🗺️ Summary Table (Memory Map Overview)

| Category                   | Commands/Concepts                                     |
| -------------------------- | ----------------------------------------------------- |
| **Group Mgmt**             | `groupadd`, `groupdel`, `usermod -aG`, `/etc/group`   |
| **Permission Mgmt**        | `chmod`, `chown`, `chgrp`, `umask`                    |
| **ACLs**                   | `setfacl`, `getfacl`, default ACLs                    |
| **Special Bits**           | `chmod +s`, `chmod +t`                                |
| **Security Practices**     | Least privilege, use groups, no `777` permissions     |
| **File Access Control**    | `rwx`, permission octals, symbolic notation           |
| **User Membership**        | Primary vs Secondary group, `id`, `groups`            |
| **Permission Inheritance** | `umask`, default ACLs, directory-specific propagation |

---

## 📌 Quick Cheatsheet

```bash
# Create a group
sudo groupadd devteam

# Add user to group
sudo usermod -aG devteam alice

# Change ownership of a file
sudo chown alice:devteam report.txt

# Set full group permissions
chmod 770 shared_dir

# View ACLs
getfacl file.txt

# Set default ACL on directory
setfacl -d -m g:devteam:rwX /project
```

---

This reference serves as a compact yet exhaustive technical map to understand and administer **Linux groups and permissions** in both standard and advanced contexts (e.g., ACLs, inheritance). Each command corresponds to a defined role in access control, allowing for modular and scalable security design.

