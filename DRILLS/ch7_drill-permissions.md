# Drill: Permissions (easy)

**Date:** 2026-10-22
**Covers:** RH124 ch. 7 (Control Access to Files), plus one review task from week 1
**Format:** one idea per task, two parts. Run everything on servera as root.

---

## Setup

```bash
groupadd sales
useradd -G sales maria
useradd -G sales victor
useradd paul
mkdir /perms
touch /perms/report.txt
echo '#!/bin/bash' > /perms/hello.sh
echo 'echo It works' >> /perms/hello.sh
```

---

## Part 1

### Task 1
The file `/perms/report.txt` must be owned by user `maria` and group `sales`.

**Verify:**
```bash
ls -l /perms/report.txt       # check the owner and group columns
```

### Task 2
On `/perms/report.txt`, the owner and the group can read and write. Other users have no access at all.

**Verify:**
```bash
su - maria -c 'echo maria >> /perms/report.txt'     # should work (no output)
su - victor -c 'echo victor >> /perms/report.txt'   # should work (no output)
su - paul -c 'cat /perms/report.txt'                # should say Permission denied
```

### Task 3
Every user on the system must be able to run `/perms/hello.sh`. Only root may change its contents.

**Verify:**
```bash
su - paul -c /perms/hello.sh                  # should print: It works
su - paul -c 'echo x >> /perms/hello.sh'      # should say Permission denied
```

### Task 4 (review, week 1)
The account `paul` must expire on 2026-12-31.

**Verify:**
```bash
chage -l paul | grep 'Account expires'
grep '^paul:' /etc/shadow | cut -d: -f8       # stored day number; 20818 = 2026-12-31
```

---

## Part 2

### Task 5
Create the directory `/perms/salesdocs`. Its group must be `sales`. The owner and the group have full access, and other users have no access.

**Verify:**
```bash
su - maria -c 'touch /perms/salesdocs/m1'     # should work
su - paul -c 'ls /perms/salesdocs'            # should say Permission denied
```

### Task 6
Files and directories created inside `/perms/salesdocs` must automatically belong to group `sales`.

**Verify:**
```bash
su - victor -c 'touch /perms/salesdocs/v1'
su - victor -c 'mkdir /perms/salesdocs/vdir'
ls -l /perms/salesdocs                        # v1 and vdir: group column should be sales
```

### Task 7
Inside `/perms/salesdocs`, users may delete only the files they own.

**Verify** (run after task 6, it uses `v1`):
```bash
su - maria -c 'rm -f /perms/salesdocs/v1'     # should say Operation not permitted
su - victor -c 'rm -f /perms/salesdocs/v1'    # should work
```

### Task 8
When `victor` logs in, the new directories he creates must get `rwxr-x---` by default. This must still be true after he logs out and back in.

**Verify:**
```bash
su - victor -c 'mkdir ~/umtest'
ls -ld /home/victor/umtest                    # should show drwxr-x---
```
If you re-run this check, delete `/home/victor/umtest` first, because `mkdir` won't replace an existing directory.

---

## Persistence

Tasks 1–7 are saved on disk and survive a reboot. Task 8 survives only if the setting is in victor's own startup file. Every `su - victor` is a fresh login, so the check tests that.

---

## Reset (to redo the drill)

```bash
userdel -r maria
userdel -r victor
userdel -r paul
groupdel sales
rm -rf /perms
```
