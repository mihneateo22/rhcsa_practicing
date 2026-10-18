# Drill review: 2026-10-18 (RH124 ch 1–6)

This review covers the **Drill 2** section of `DRILL1_ch1-6.txt`. It was done on **serverb**, so task 8 was reversed: servera's release string was fetched from serverb.

- **Time:** about 95 minutes (limit: 60)
- **Notes used:** 4 · **Solutions given:** 3

```
Week 1 | Drill 2 | ~95 min / 60 | Notes: 4 | Solutions: 3
Weak: restricted sudo, man index (mandb / man -f), /etc/profile.d, remote command over SSH
Strong: account lockout, globs, symlinks, chage basics, stdout/stderr redirection
Next focus: find every weak item in man pages, then redo Drill 2 with no notes
```

---

## Done without help

- dana: UID, full name, home directory, password
- bogdan locked out (`usermod -L -e 1`), right on the first try
- Moving files with globs, and the `/root/latest` symlink
- cristi's minimum and maximum password age
- dana's account expiry 90 days from today
- dana's `today` alias
- stdout and stderr split into `out.txt` and `err.txt`
- Found `usermod -rG` on my own

---

## Needed my notes (4)

### 1. Primary group (task 1)

**Requirement:** dana's primary group is `auditors`.

**The concept:** a user has exactly **one** primary group, so you set it rather than add to it. Lowercase `-g` sets the primary group; uppercase `-G` sets supplementary groups.

**Correct way, when creating the user:**

```bash
useradd -u 3100 -c "Dana Ionescu" -g auditors -d /srv/dana dana
```

**Trap I hit:** I created dana first and changed her primary group afterwards. `useradd` had already created a private group `dana` (GID 3100), and `usermod -g` doesn't delete it. Fix:

```bash
usermod -g auditors dana
groupdel dana
```

**Verify:** `id dana` shows `gid=4200(auditors)`, and `getent group dana` prints nothing.

**Find it offline:** `man usermod`, then `/--gid`

### 2. Restricted sudo and removing admin rights (task 2)

**Requirement:** dana may run only `useradd` and `passwd` as root, and may not change root's password. radu loses his admin rights but stays in `finance`.

**Correct way:**

```bash
echo 'dana ALL=/usr/sbin/useradd, /usr/bin/passwd, !/usr/bin/passwd root' > /etc/sudoers.d/dana
chmod 440 /etc/sudoers.d/dana
visudo -c
usermod -rG wheel radu        # or: gpasswd -d radu wheel
```

**Concepts:**
- `%wheel ALL=(ALL) ALL` is in `/etc/sudoers` by default. radu was an admin **only because he was in wheel**, so removing him from wheel removes his admin rights. Don't edit the `%wheel` line; other admins depend on it.
- sudo rules need **full paths**. Find them with `which useradd` and `which passwd`.

**Traps I hit:**
- **Double quotes with `!`:** `echo "...!/usr/bin/passwd root"` triggers bash history expansion ("event not found"), and nothing gets written. Use **single quotes**, or write the rule with `visudo -f /etc/sudoers.d/dana`, which avoids shell quoting entirely and checks the syntax when you save.
- **`>>` keeps broken lines from earlier attempts.** Use `>` for a single-rule file.
- **`usermod -G` without `-a` replaces all supplementary groups.** Use `usermod -rG` or `gpasswd -d` to remove one group.
- **`-r` means something different in each tool:** `usermod -r` removes groups (only with `-G`); `userdel -r` deletes the home directory; `useradd -r` creates a system account.

**Verify:** `sudo -l -U dana` lists only the two commands; `sudo -l -U radu` says he's not allowed to run sudo; `id radu` still shows `finance`.

**Find it offline:** `man 5 sudoers`, then `/EXAMPLES`

### 3. Account inactive after the password expires (task 3)

**Requirement:** cristi's account becomes inactive if he hasn't changed an expired password within 7 days.

**Correct way:**

```bash
chage -m 2 -M 45 -I 7 cristi
```

**Concept:** after the password expires, the user has a grace period in which they can log in only to change it. `-I` sets how long that grace period is. When it runs out, the account becomes inactive.

**Trap I almost hit:** `chage -d 30` sets the **last password change** to 30 days after 1 January 1970 (that is, 31 January 1970), which makes the password expire immediately. `-I` is an uppercase i (**I**nactive), not a lowercase L.

**Verify:** in `chage -l cristi`, "Password inactive" is exactly 7 days after "Password expires".

**Find it offline:** `man chage`, then `/INACTIVE`

### 4. Copy with permissions and timestamps preserved (task 5)

**Requirement:** `/root/skel-backup` contains every file from `/etc/skel`, with the original permissions and timestamps.

**Correct way:**

```bash
cp -a /etc/skel /root/skel-backup        # /root/skel-backup must NOT exist yet
```

**Traps:**
- Every file in `/etc/skel` is hidden (starts with a dot). `*` doesn't match hidden files, so `cp -a /etc/skel/* dest` copies nothing. Copy the directory itself, or use `/etc/skel/.` to mean "everything inside, including hidden files".
- If the destination directory already exists, `cp -a /etc/skel /root/skel-backup` creates `/root/skel-backup/skel` instead.

**Verify:** `ls -la /root/skel-backup` shows the same dates as `/etc/skel` (Oct 29 2024), not today's date.

**Find it offline:** `cp --help | grep -i preserve`

---

## Needed the solution (3)

### 5. History timestamps for every user (task 6)

```bash
echo 'export HISTTIMEFORMAT="%F %T "' > /etc/profile.d/histtime.sh
```

**Concepts:**
- `history` always records when each command ran, but it only displays the time if `HISTTIMEFORMAT` is set. The format codes are the same as for `date`: `%F` is the date and `%T` is the time.
- Settings for **all users** go in `/etc/profile.d/*.sh`. Every login shell reads those files (through `/etc/profile`). Settings for **one user** go in that user's `~/.bashrc`.
- **Quoting:** the outer single quotes write the line to the file exactly as typed. The inner double quotes keep the trailing space as part of the value.

**Verify:** run `su - cristi`, type a few commands, then `history | tail -n 3`. Every line starts with a date and time. It has to be a fresh login; an already-open shell won't show the change.

**Find it offline:** `man bash`, then `/HISTTIMEFORMAT`; `head -n 20 /etc/profile` (the comments there say to use `profile.d`).

### 6. Manual page descriptions (task 8)

```bash
mandb
man -f passwd > /root/passwd-pages.txt
```

**Concepts:**
- "nothing appropriate" means the man page index hasn't been built yet. `mandb` builds it.
- `man -f` (the same as `whatis`) matches the **exact page name**. `man -k` (the same as `apropos`) matches any word in page names **and** descriptions, so it also returns `chpasswd`, `gpasswd` and so on.

**Find it offline:** `man man`, then `/-f`

### 7. Running a command on another host over SSH (task 8, reversed)

```bash
ssh mihnea@servera cat /etc/redhat-release > /root/servera-release.txt
```

**Concepts:**
- The command after the host name runs **on the remote host**.
- The `>` is handled by the **local** shell, so the file is saved on the machine you're typing on.
- To create the file on the remote machine instead, quote the whole remote command: `ssh user@host 'cmd > file'`.

**Find it offline:** `man ssh`, then look at the SYNOPSIS. The `[command]` at the end is the remote command.

---

## Other traps from today

- **`groupmod: GID '5000' already exists`:** a leftover group from earlier practice. Find the owner with `getent group 5000`. On the real exam, an ID given in a task is always free; if it isn't, an earlier step of yours took it.
- **`-bash: err.txt: Permission denied`:** the message starts with `-bash:`, so it was **bash** that couldn't create the file, not `cat`. `/srv/dana` existed but was owned by root. Fix:

  ```bash
  cp -a /etc/skel/. /srv/dana/
  chown -R dana:auditors /srv/dana
  ```

- **Testing an expired account with `su -` as root proves nothing.** `/etc/pam.d/su` skips the account check when root runs `su`. Check `chage -l user | grep 'Account expires'` instead.
- **Lab hygiene:** my "clean" snapshot of servera wasn't actually clean. Revert, check with `getent group 5000` and `id <practice users>`, then take a fresh snapshot.

---

## Man page lookup table (exam skill: no notes)

| Weak spot | Where to find it offline |
|---|---|
| Primary group | `man usermod`, then `/--gid` |
| Account inactive after expiry | `man chage`, then `/INACTIVE` |
| Copy keeping permissions and timestamps | `cp --help \| grep -i preserve` |
| Restricted sudo rule | `man 5 sudoers`, then `/EXAMPLES` |
| Exact man page name | `man man`, then `/-f` |
| History timestamps | `man bash`, then `/HISTTIMEFORMAT` |
| Settings for all users | `head -n 20 /etc/profile` |
| Command on a remote host | `man ssh`, then look at the SYNOPSIS |

---

## Next steps

1. Go through the lookup table on servera, typing each search and finding the answer myself (about 20 minutes).
2. Redo Drill 2 from `DRILL1_ch1-6.txt` with **no notes**, only `man`.
3. Before Sim 1 (Oct 21): **LEARN sudo**.
