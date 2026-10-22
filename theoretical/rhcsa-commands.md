# RHCSA Command Reference

**Mihnea · RHCSA EX200 (RHEL 10) · RH124 · Last updated: 2026-10-22**

Covers **RH124 chapters 1–8**. Add one section per chapter using the template at the end of this file.

Every chapter has the same three parts: **Commands** (what you type), **Key files** (where the config lives), and **Exam notes** (traps, how to verify, whether it survives a reboot).

> **On the exam you only have:** `man`, `info`, `cmd --help`, and `/usr/share/doc/`. This file is for building muscle memory. Practise finding everything in it offline too.

---

## Chapter 1 — Get Started with Red Hat Enterprise Linux

Mostly concepts, very few commands.

| Command | What it does | Example |
|---|---|---|
| `cat /etc/redhat-release` | Show the RHEL release you are running | `Red Hat Enterprise Linux release 10.x` |
| `cat /etc/os-release` | Distro name and version (works on Rocky/Alma too) | `grep VERSION /etc/os-release` |
| `uname -r` | Running kernel version | `uname -r` |

**Concepts to remember**

- Open source: you can use, study, modify and redistribute the code.
- Upstream chain: **Fedora → CentOS Stream → RHEL**. Rocky and Alma are free RHEL rebuilds, fine for drills.
- A *distribution* = Linux kernel + userspace tools + package manager + installer, tested together.

---

## Chapter 2 — Access the Command Line

### Commands

| Command | What it does | Example |
|---|---|---|
| `ssh user@host` | Log in to a remote system | `ssh student@serverb` |
| `ssh -i key user@host` | Log in with a specific private key | `ssh -i ~/lab.pem student@servera` |
| `exit` / `Ctrl+D` | Log out of the shell | `exit` |
| `whoami` | Which user am I? | `whoami` |
| `date` | Date and time, custom formats | `date +%R` (HH:MM), `date +%F` (YYYY-MM-DD) |
| `passwd` | Change **your own** password | `passwd` |
| `file` | Detect a file's type | `file /etc/passwd` |
| `cat` | Print one or more files | `cat file1 file2` |
| `less` | Page through a file | `less /etc/services` |
| `head -n N` | First N lines (default 10) | `head -n 5 /etc/passwd` |
| `tail -n N` | Last N lines (default 10) | `tail -n 3 /etc/passwd` |
| `tail -f` | Follow a file as it grows (Ctrl+C to stop) | `tail -f /var/log/messages` |
| `wc` | Count lines / words / bytes | `wc -l /etc/passwd` |
| `history` | Numbered list of past commands | `history \| tail` |
| `!N` / `!string` / `!!` | Re-run command N / last one starting with *string* / last command | `!26`, `!ls`, `sudo !!` |
| `cmd1; cmd2` | Run commands one after another | `date; whoami` |
| `\` at end of line | Continue a long command on the next line | `head -n 3 \`, Enter, then `/etc/passwd` |

### Keyboard shortcuts

| Keys | Action |
|---|---|
| `Tab` / `Tab Tab` | Complete a command, file name **or option** / list the possibilities |
| `Ctrl+A` / `Ctrl+E` | Jump to start / end of line |
| `Ctrl+U` / `Ctrl+K` | Delete from cursor to start / to end of line |
| `Ctrl+←` / `Ctrl+→` | Jump one word left / right |
| `Ctrl+R` | Search history backwards (press again for older matches) |
| `Esc .` or `Alt+.` | Insert the last argument of the previous command |
| `Ctrl+C` / `Ctrl+L` | Cancel the current command / clear the screen |

### Desktop (GNOME)

- `Super` key opens **Activities**; type `terminal` to open one.
- `Ctrl+Alt+F3` … `F6` switch to text virtual consoles; `Ctrl+Alt+F2` goes back to the graphical session.

### Exam notes

> - `Tab Tab` also completes **long options**: `useradd --<Tab><Tab>` shows every option. Faster than opening the man page.
> - `ssh -i` refuses a private key that others can read: fix with `chmod 600 key`.
> - First SSH connection asks you to accept the host key. It is then stored in `~/.ssh/known_hosts`.

---

## Chapter 3 — Manage Files from the Command Line

### File system hierarchy (FHS)

| Directory | Contents |
|---|---|
| `/` | Root of the whole tree |
| `/boot` | Kernel and boot loader files |
| `/dev` | Device files (`/dev/sda`, `/dev/null`) |
| `/etc` | System configuration files |
| `/home` | Regular users' home directories |
| `/root` | root's home directory |
| `/run` | Runtime data since last boot (wiped at reboot) |
| `/tmp` | Temporary files, world-writable (old files cleaned automatically) |
| `/usr` | Installed software: `/usr/bin`, `/usr/sbin`, `/usr/lib`, `/usr/share` |
| `/usr/local` | Locally built software |
| `/var` | Data that changes and persists across reboots: logs, caches, spools |

`/bin`, `/sbin`, `/lib` are symlinks into `/usr`.

### Navigate and list

| Command | What it does | Example |
|---|---|---|
| `pwd` | Print current directory | `pwd` |
| `cd dir` | Change directory | `cd /etc` |
| `cd` / `cd ~` | Go to your home | `cd` |
| `cd -` | Go back to the previous directory | `cd -` |
| `cd ..` | Go up one level | `cd ../..` |
| `ls -l` | Long listing (perms, owner, size, date) | `ls -l /etc/passwd` |
| `ls -a` | Include hidden (dot) files | `ls -la ~` |
| `ls -R` | List recursively | `ls -R /etc/sysconfig` |
| `ls -d` | Show the directory itself, not its contents | `ls -ld /tmp` |
| `ls -i` | Show inode numbers | `ls -i file1 file2` |
| `ls -h` | Human-readable sizes (with `-l`) | `ls -lh` |

Absolute path starts with `/` (`/home/student/file`). Relative path starts from where you are (`../docs/file`).

### Create, copy, move, delete

| Command | What it does | Example |
|---|---|---|
| `touch` | Create an empty file / update its timestamp | `touch notes.txt` |
| `mkdir` | Create a directory | `mkdir reports` |
| `mkdir -p` | Create a whole path, no error if it exists | `mkdir -p proj/2026/q4` |
| `cp src dst` | Copy a file | `cp /etc/hosts ~/hosts.bak` |
| `cp -r` | Copy a directory recursively | `cp -r dir1 dir2` |
| `cp -a` | Archive copy: keeps perms, owner, timestamps, SELinux context | `cp -a /etc/skel /tmp/skel` |
| `mv` | Move or rename | `mv old.txt new.txt` |
| `rm` | Delete a file | `rm file` |
| `rm -r` | Delete a directory and everything in it | `rm -r olddir` |
| `rm -i` / `rm -f` | Ask before each delete / never ask | `rm -ri dir` |
| `rmdir` | Delete an **empty** directory | `rmdir emptydir` |

### Links

| Command | What it does | Example |
|---|---|---|
| `ln target link` | Hard link (another name for the same inode) | `ln file1 file1-hard` |
| `ln -s target link` | Symbolic (soft) link (a pointer to a path) | `ln -s /etc/hosts ~/hosts-link` |
| `ls -li` | Compare inode numbers and link counts | `ls -li file1 file1-hard` |

| | Hard link | Symbolic link |
|---|---|---|
| Points to | The inode (data) | A path name |
| Across file systems | No | Yes |
| To a directory | No | Yes |
| Original deleted | Data still reachable | Link breaks ("dangling") |

### Shell expansions

| Pattern | Matches / expands to | Example |
|---|---|---|
| `*` | Any string, including empty | `ls *.conf` |
| `?` | Exactly one character | `ls file?.txt` |
| `[abc]` | One character from the set | `ls file[123]` |
| `[!abc]` or `[^abc]` | One character **not** in the set | `ls [!a]*` |
| `[[:alpha:]]` `[[:digit:]]` `[[:upper:]]` `[[:lower:]]` `[[:alnum:]]` `[[:space:]]` `[[:punct:]]` | Character classes | `ls [[:upper:]]*` |
| `{a,b,c}` | Brace list | `mkdir -p dir/{logs,data,tmp}` |
| `{1..5}` / `{a..e}` | Brace range | `touch file{1..5}.txt` |
| `~` / `~user` | Your home / user's home | `ls ~student` |
| `$VAR` / `${VAR}` | Variable value | `echo ${HOME}` |
| `$(cmd)` | Command substitution (output of cmd) | `echo "Today is $(date +%A)"` |

| Quoting | Effect | Example |
|---|---|---|
| `"..."` | Stops globbing and word splitting; `$VAR` and `$(...)` still expand | `echo "$HOME *"` → `/home/student *` |
| `'...'` | Stops **all** expansion | `echo '$HOME'` → `$HOME` |
| `\` | Escapes the next single character | `echo \$HOME` → `$HOME` |

### Exam notes

> - Use **absolute targets for symlinks** (`ln -s /data/file /opt/link`). A relative target is resolved from the link's directory, not from where you ran the command.
> - `cp -r dir1 dir2`: if `dir2` already exists you get `dir2/dir1`, not a copy into `dir2`. Check with `ls -R` afterwards.
> - Test a glob safely before deleting: run `ls pattern` first, then `rm pattern`.
> - Files and links are on disk, so they survive a reboot. Anything under `/run` or `/tmp` may not.

---

## Chapter 4 — Get Help in Red Hat Enterprise Linux

### Commands

| Command | What it does | Example |
|---|---|---|
| `man cmd` | Open the manual page | `man useradd` |
| `man N topic` | Open a specific section | `man 5 passwd` (the file format, not the command) |
| `man -k word` | Search page names + descriptions (same as `apropos`) | `man -k password` |
| `man -f cmd` | Which sections have this page (same as `whatis`) | `man -f passwd` |
| `man -K word` | Search the **full text** of all pages (slow, asks per hit) | `man -K NOPASSWD` |
| `mandb` | Rebuild the index used by `man -k` | `sudo mandb` |
| `cmd --help` | Short usage summary *(allowed on the exam)* | `chage --help` |
| `info cmd` | GNU info docs *(allowed on the exam)* | `info coreutils` |
| `ls /usr/share/doc/` | Package docs and example configs *(allowed on the exam)* | `ls /usr/share/doc/sudo` |

### Man page sections

| Section | Contains |
|---|---|
| **1** | User commands (`ls`, `passwd`) |
| 2 / 3 | System calls / library functions (C programming) |
| 4 | Special files (`/dev/*`) |
| **5** | File formats and config files (`passwd`, `fstab`, `sudoers`) |
| 6 | Games |
| 7 | Conventions and overviews (`regex`, `signal`) |
| **8** | System administration commands (`useradd`, `mount`) |

### Moving around inside `man` (it uses `less`)

| Key | Action |
|---|---|
| `Space` / `b` | Page down / up |
| `/text` | Search forward; `n` next match, `N` previous |
| `g` / `G` | Jump to start / end |
| `q` | Quit |

### Exam notes

> - `man -k` says *"nothing appropriate"* → the index isn't built yet. Run `mandb` once, then retry.
> - Filter by section: `man -k user | grep '(8)'` lists only admin commands about users.
> - Most pages have an **EXAMPLES** section near the end: open the page and type `/EXAMPLE`.
> - Before using a config file format from memory, check `man 5 <file>`. For sudo: `man 5 sudoers`, then `/EXAMPLE`.

---

## Chapter 5 — Create, View, and Edit Text Files

### Redirection and pipes

Streams: **0** = stdin, **1** = stdout, **2** = stderr.

| Syntax | What it does |
|---|---|
| `cmd > file` | stdout to file (**overwrites**) |
| `cmd >> file` | stdout to file (**appends**) |
| `cmd 2> file` | stderr to file |
| `cmd 2> /dev/null` | Throw errors away |
| `cmd &> file` | stdout **and** stderr to file (`&>>` appends) |
| `cmd > file 2>&1` | Same as `&>`, classic form (order matters) |
| `cmd < file` | Use file as stdin |
| `cmd1 \| cmd2` | Pipe: stdout of cmd1 becomes stdin of cmd2 |
| `cmd \| less` / `\| head` / `\| wc -l` | Page / trim / count output |
| `cmd \| tee file` | Show output **and** save it (`tee -a` appends) |
| `cmd \| sudo tee /etc/file` | Write a root-owned file from a normal user |

### Vim

| Keys | Action |
|---|---|
| `vim file` | Open (or create) a file |
| `i` / `a` / `o` / `O` | Insert before cursor / after cursor / new line below / new line above |
| `Esc` | Back to command (normal) mode |
| `:w` / `:q` / `:wq` (or `:x`) / `:q!` | Save / quit / save and quit / quit **without** saving |
| `u` / `Ctrl+R` | Undo / redo |
| `x` | Delete character |
| `dd` / `5dd` | Cut line / cut 5 lines |
| `yy` / `5yy` | Copy (yank) line / 5 lines |
| `p` / `P` | Paste after / before |
| `v` / `V` / `Ctrl+V` | Visual select: characters / lines / block |
| `gg` / `G` / `:15` | Go to first line / last line / line 15 |
| `0` / `$` | Start / end of line |
| `/text` then `n` / `N` | Search forward, next / previous match |
| `:%s/old/new/g` | Replace every *old* with *new* in the whole file |
| `:set nu` | Show line numbers |
| `vimtutor` | 30-minute interactive vim lesson (shell command) |

### Shell variables and environment

| Command | What it does | Example |
|---|---|---|
| `VAR=value` | Set a shell variable (no spaces around `=`) | `COUNT=40` |
| `echo $VAR` | Read it | `echo $COUNT` |
| `export VAR` | Make it an environment variable (child processes see it) | `export EDITOR=vim` |
| `VAR=x cmd` | Set a variable for one command only | `LANG=fr_FR.UTF-8 date` |
| `unset VAR` | Remove the variable | `unset COUNT` |
| `export -n VAR` | Un-export, keep as shell variable | `export -n EDITOR` |
| `set \| less` / `env` | All shell variables / only environment variables | `env \| grep PATH` |
| `alias name='cmd'` | Create an alias; `alias` lists, `unalias` removes | `alias ll='ls -l'` |
| `which cmd` / `type cmd` | Where a command lives / is it an alias, builtin, or file | `type ll` |
| `source file` (or `. file`) | Re-read a config file in the current shell | `source ~/.bashrc` |

| Variable | Meaning |
|---|---|
| `PATH` | Directories searched for commands (`~/bin` and `~/.local/bin` are included by default on RHEL) |
| `HOME`, `USER`, `SHELL`, `PWD` | Home dir, user name, login shell, current dir |
| `PS1` | Prompt format |
| `HISTFILESIZE`, `HISTTIMEFORMAT` | History length, timestamps in `history` (`HISTTIMEFORMAT="%F %T "`) |
| `LANG` | Language / locale |
| `EDITOR` | Default editor used by other tools |

### Key files

| File | Read when | Use for |
|---|---|---|
| `~/.bashrc` | Every interactive shell | Your aliases, functions, variables |
| `~/.bash_profile` | Login shells | Login-time settings (it also sources `~/.bashrc`) |
| `/etc/profile`, `/etc/bashrc`, `/etc/profile.d/*.sh` | Same, but for **all** users | System-wide settings |

### Exam notes

> - Variables and aliases typed at the prompt **disappear at logout**. To make them persistent, put them in `~/.bashrc` (one user) or a file in `/etc/profile.d/` (everyone).
> - `sudo echo text > /etc/file` fails: the `>` is done by **your** shell, not by sudo. Use `echo text | sudo tee /etc/file`.
> - `cmd 2>&1 > file` is wrong: stderr still goes to the screen. Redirect stdout first: `cmd > file 2>&1`.
> - `sort file > file` empties the file (the shell truncates it before `sort` reads it). Write to a new file.

---

## Chapter 6 — Manage Local Users and Groups

### Key files

| File | Format |
|---|---|
| `/etc/passwd` | `name:x:UID:GID:GECOS(comment):home:shell` |
| `/etc/shadow` | `name:hash:lastchange:min:max:warn:inactive:expire:` (days; root-only) |
| `/etc/group` | `name:x:GID:member1,member2` (lists **supplementary** members only) |
| `/etc/gshadow` | Group passwords and admins |
| `/etc/login.defs` | Defaults for **new** users: `PASS_MAX_DAYS`, `PASS_MIN_DAYS`, `PASS_WARN_AGE`, `UID_MIN`, `CREATE_HOME` |
| `/etc/default/useradd` | `useradd` defaults: shell, home base, skel (`useradd -D` shows them) |
| `/etc/skel/` | Files copied into every new home directory |
| `/etc/sudoers` | sudo rules (edit only with `visudo`) |
| `/etc/sudoers.d/` | Drop-in sudo rule files (preferred) |

**Hash field in `/etc/shadow`:** `$6$...` = SHA-512, `$y$...` = yescrypt. A leading `!` = password locked or never set.

**UID ranges:** `0` root · `1–200` system (static, Red Hat) · `201–999` system (dynamic) · `1000+` regular users. GIDs follow the same scheme.

**Primary group** = the GID in `/etc/passwd` (new files get this group). By default each user gets a private group with the same name. **Supplementary groups** = extra memberships listed in `/etc/group`.

### Who am I / who is that

| Command | What it does | Example |
|---|---|---|
| `id` / `id user` | UID, GID and all groups | `id harry` |
| `groups user` | Group names only | `groups harry` |
| `getent passwd user` | The user's `/etc/passwd` line | `getent passwd harry` |
| `getent group grp` | The group's `/etc/group` line | `getent group wheel` |

### Become root

| Command | What it does | Example |
|---|---|---|
| `su -` | Root **login** shell (root's environment; asks root's password) | `su -` |
| `su - user` | Login shell as another user | `su - harry` |
| `su - user -c 'cmd'` | Run one command as another user | `su - harry -c 'id'` |
| `sudo cmd` | Run one command as root (asks **your** password) | `sudo useradd bob` |
| `sudo -i` | Root login shell via sudo | `sudo -i` |
| `sudo -u user cmd` | Run as a user other than root | `sudo -u harry whoami` |
| `sudo -l` | What may **I** run with sudo? | `sudo -l` |
| `sudo -l -U user` | What may **user** run? (run as root) | `sudo -l -U harry` |
| `visudo` | Safely edit `/etc/sudoers` (syntax check on save) | `visudo` |
| `visudo -f file` | Safely edit a drop-in file | `visudo -f /etc/sudoers.d/harry` |
| `visudo -c` | Check syntax of sudoers + all drop-ins | `visudo -c` |

### sudoers rule syntax: `WHO  HOSTS=(RUN_AS)  COMMANDS`

| Rule | Meaning |
|---|---|
| `%wheel ALL=(ALL) ALL` | Members of **wheel** can run anything (enabled by default) |
| `harry ALL=(ALL) NOPASSWD: ALL` | harry runs anything **without** a password |
| `%admins ALL=(ALL) ALL` | `%` = a group |
| `amy ALL=/usr/sbin/useradd, /usr/bin/passwd, !/usr/bin/passwd root` | Only these commands, and not changing root's password |

### Manage users

| Command | What it does | Example |
|---|---|---|
| `useradd user` | Create a user (+ private group + home from `/etc/skel`) | `useradd harry` |
| `useradd -u UID` | Set a specific UID | `useradd -u 2001 sarah` |
| `useradd -g grp` | Set the **primary** group | `useradd -g devs tom` |
| `useradd -G g1,g2` | Set **supplementary** groups | `useradd -G wheel,devs ana` |
| `useradd -c "text"` | Comment / full name (GECOS) | `useradd -c "Ana Pop" ana` |
| `useradd -s shell` | Login shell | `useradd -s /sbin/nologin svc1` |
| `useradd -d dir -m` | Custom home directory, create it | `useradd -d /srv/web -m web` |
| `useradd -D` | Show default settings | `useradd -D` |
| `usermod -aG grp user` | **Add** a supplementary group (keeps the others) | `usermod -aG wheel harry` |
| `usermod -G grp user` | **Replace** all supplementary groups | `usermod -G devs harry` |
| `usermod -g grp user` | Change primary group | `usermod -g devs harry` |
| `usermod -c / -s / -u` | Change comment / shell / UID | `usermod -s /sbin/nologin harry` |
| `usermod -d dir -m user` | Move home directory | `usermod -d /home/h2 -m harry` |
| `usermod -l new old` | Rename the login | `usermod -l harold harry` |
| `usermod -L` / `-U` | Lock / unlock the password | `usermod -L harry` |
| `usermod -e DATE` | Account expiry date | `usermod -e 2026-12-31 harry` |
| `userdel user` | Delete user, **keep** home and mail | `userdel harry` |
| `userdel -r user` | Delete user **and** home + mail spool | `userdel -r harry` |
| `find / -nouser` | Find files whose owner no longer exists | `find / -nouser -o -nogroup` |

### Manage groups

| Command | What it does | Example |
|---|---|---|
| `groupadd grp` | Create a group | `groupadd devs` |
| `groupadd -g GID grp` | Create with a specific GID | `groupadd -g 30000 sales` |
| `groupadd -r grp` | Create a system group (GID < 1000) | `groupadd -r appsvc` |
| `groupmod -n new old` | Rename a group | `groupmod -n engineers devs` |
| `groupmod -g GID grp` | Change GID | `groupmod -g 30001 sales` |
| `groupdel grp` | Delete a group (fails if it's someone's primary group) | `groupdel sales` |
| `gpasswd -d user grp` | Remove one user from one group | `gpasswd -d harry devs` |

### Passwords and aging

| Command | What it does | Example |
|---|---|---|
| `passwd user` | Set a user's password (as root) | `passwd harry` |
| `passwd --stdin user` | Set the password from stdin, non-interactive (Red Hat option) | `echo 'Redhat123' \| passwd --stdin harry` |
| `chpasswd` | Same idea, `user:password` pairs | `echo 'harry:Redhat123' \| chpasswd` |
| `passwd -S user` | Short password status | `passwd -S harry` |
| `chage -l user` | Show all aging settings | `chage -l harry` |
| `chage -d 0 user` | Force password change at next login | `chage -d 0 harry` |
| `chage -M N user` | Max days a password is valid | `chage -M 90 harry` |
| `chage -m N user` | Min days between changes | `chage -m 7 harry` |
| `chage -W N user` | Warning days before expiry | `chage -W 14 harry` |
| `chage -I N user` | Inactive days after expiry before the account locks | `chage -I 5 harry` |
| `chage -E DATE user` | Account expiry date (`-E -1` removes it) | `chage -E 2026-12-31 harry` |
| `date -d "+N days" +%F` | Compute a date for `-E` | `chage -E $(date -d "+30 days" +%F) harry` |

### Exam notes

> - **`usermod -G` without `-a` wipes the user's other supplementary groups.** Always `usermod -aG`. Verify with `id user`.
> - **sudo task** ("harry runs anything as root without a password"): `echo 'harry ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/harry`, then `visudo -c`. Verify with `sudo -l -U harry`, or `su - harry -c 'sudo -n id'` (should print `uid=0(root)`).
> - Files in `/etc/sudoers.d/` whose name contains a `.` or ends in `~` are **ignored**. Name it `harry`, not `harry.conf`.
> - **`/etc/login.defs` only affects users created afterwards.** "All new users' passwords expire after 90 days" → `PASS_MAX_DAYS 90` there. Existing users → `chage -M 90 user`.
> - "User must change password at first login" → `chage -d 0 user`. Check with `chage -l user` (shows *password must be changed*).
> - `usermod -L` only locks the **password**; SSH key logins still work. To block everything, also expire the account: `usermod -L -e 1 user` (this is what `man usermod` recommends).
> - `su -` needs root's password. If root has none (you skipped it at install), use `sudo -i`, or set one with `sudo passwd root`.
> - "User has no interactive shell" → `-s /sbin/nologin`. Check the last field of `getent passwd user`.
> - **RHEL 9 vs 10:** on RHEL 10 `passwd` is provided by the `shadow-utils` package instead of its own package. `--stdin` still works; `chpasswd` is the fallback.
> - Everything in this chapter writes to files in `/etc`, so it **survives a reboot** automatically.

---

## Chapter 7 — Control Access to Files

### Read a permission string

`ls -l` → `-rw-r-----. 1 harry devs 512 Oct 19 10:00 report.txt`

| Part | In the example | Meaning |
|---|---|---|
| 1st character | `-` | Type: `-` file · `d` directory · `l` symlink · `b`/`c` device |
| Characters 2–4 | `rw-` | **User** (owner) permissions |
| Characters 5–7 | `r--` | **Group** permissions |
| Characters 8–10 | `---` | **Other** permissions (everyone else) |
| Character 11 | `.` | `.` = SELinux context set · `+` = ACL set |
| Columns 3–4 | `harry devs` | User owner, group owner |

### What `r`, `w`, `x` mean

| Bit | Value | On a file | On a directory |
|---|---|---|---|
| `r` | 4 | Read the contents | List the names inside (`ls`) |
| `w` | 2 | Change the contents | Create, delete, rename files inside (needs `x` too) |
| `x` | 1 | Run it as a program | Enter it (`cd`) and open files inside by name |

**Which bits apply to you — first match wins, the rest are ignored:**

1. You **own** the file → only the **user** bits count.
2. Otherwise, you are in the file's **group** → only the **group** bits count.
3. Otherwise → the **other** bits count.

root skips the `r`/`w` checks (it still needs at least one `x` bit to run a file).

### Change permissions: `chmod`

Symbolic form: `chmod WHO OP PERMS file`

| Part | Values |
|---|---|
| WHO | `u` user · `g` group · `o` other · `a` all |
| OP | `+` add · `-` remove · `=` set exactly (removes the rest) |
| PERMS | `r` `w` `x` · `X` · `s` · `t` |

| Command | What it does | Example |
|---|---|---|
| `chmod u+x` | Add execute for the owner | `chmod u+x backup.sh` |
| `chmod go-rwx` | Remove all access for group and other | `chmod go-rwx secret.txt` |
| `chmod u=rw,g=r,o=` | Set exact permissions (`o=` = none) | `chmod u=rw,g=r,o= report.txt` |
| `chmod NNN` | Octal: one digit each for user, group, other | `chmod 750 /data/proj` |
| `chmod -R` | Apply to a directory and everything in it | `chmod -R g+rw /data/proj` |
| `chmod -R ... X` | `X` = execute only on directories (and files that already have an `x`) | `chmod -R o+rX /srv/www` |
| `stat -c '%a %A %U:%G %n'` | Octal + symbolic mode, owner, group | `stat -c '%a %A %U:%G %n' /data/proj` |

Octal = add the values per class: `rwx` = 7, `rw-` = 6, `r-x` = 5, `r--` = 4, `---` = 0.

| Octal | Symbolic | Typical use |
|---|---|---|
| `600` | `rw-------` | Private file, SSH private key |
| `640` | `rw-r-----` | File the group may read |
| `644` | `rw-r--r--` | Normal file |
| `700` | `rwx------` | Private directory, `~/.ssh` |
| `750` | `rwxr-x---` | Directory the group may enter and read |
| `755` | `rwxr-xr-x` | Normal directory or script |
| `770` | `rwxrwx---` | Shared group directory |

### Change ownership: `chown` and `chgrp`

| Command | What it does | Example |
|---|---|---|
| `chown user file` | Change the owner (root only) | `chown harry report.txt` |
| `chown user:group file` | Change owner and group in one go | `chown harry:devs report.txt` |
| `chown :group file` | Change the group only | `chown :devs report.txt` |
| `chgrp group file` | Change the group only | `chgrp devs report.txt` |
| `chown -R` | Recursive | `chown -R harry:devs /data/proj` |

- Only **root** can give a file to another owner.
- The owner can change the group, but only to a group **they belong to**.
- Separate user and group with `:`. The old `.` form (`harry.devs`) is deprecated.

### Special permissions

| Permission | Symbolic | Octal | On a file | On a directory |
|---|---|---|---|---|
| setuid | `u+s` | `4` | Runs as the file's **owner** (e.g. `/usr/bin/passwd` runs as root) | No effect |
| setgid | `g+s` | `2` | Runs as the file's **group** | New files and subdirectories **inherit the directory's group** |
| sticky | `o+t` | `1` | No effect | Users can delete or rename only **their own** files (e.g. `/tmp`) |

The special digit goes **in front** of the normal three:

| Command | Result in `ls -ld` |
|---|---|
| `chmod 2770 /shared/devs` | `drwxrws---` (setgid + `rwxrwx---`) |
| `chmod 1777 /scratch` | `drwxrwxrwt` (sticky, like `/tmp`) |
| `chmod 3770 /shared/devs` | `drwxrws--T` (setgid + sticky) |
| `chmod g-s /shared/devs` | setgid removed |

They show in the `x` slot: lowercase `s`/`t` = special bit **and** `x` set. Capital `S`/`T` = special bit set but **no** `x` underneath. A capital `S` is almost always a mistake; the `T` in `3770` is expected.

### Default permissions: `umask`

New files start at **666** (`rw-rw-rw-`), new directories at **777** (`rwxrwxrwx`). The umask lists the bits to **remove**. New files never get `x` automatically.

| umask | New file | New directory |
|---|---|---|
| `0022` | `644` `rw-r--r--` | `755` `rwxr-xr-x` |
| `0002` | `664` `rw-rw-r--` | `775` `rwxrwxr-x` |
| `0027` | `640` `rw-r-----` | `750` `rwxr-x---` |
| `0077` | `600` `rw-------` | `700` `rwx------` |

| Command | What it does | Example |
|---|---|---|
| `umask` | Show the current umask | `umask` → `0022` |
| `umask -S` | Show what is **allowed**, symbolically | `umask -S` → `u=rwx,g=rx,o=rx` |
| `umask NNN` | Change it for **this shell only** | `umask 027` |
| `su - user -c umask` | Check another user's umask after login | `su - harry -c umask` |

It's masking, not subtraction: umask `033` on a new file gives `644`, not `633`.

**Default on RHEL 9/10:** `0022` for root and regular users at login (RHEL 8 gave regular users `0002`). Confirm with `umask` on your VM.

**Make it permanent**

| Scope | How |
|---|---|
| One user | Add `umask 027` to that user's `~/.bashrc` (login shells read it too, via `~/.bash_profile`) |
| All users | Create `/etc/profile.d/umask.sh` containing `umask 027` |

### Find files by owner or permission

| Command | What it does | Example |
|---|---|---|
| `find dir -user u` | Files owned by user *u* | `find / -user harry -type f 2>/dev/null` |
| `find dir -group g` | Files with group *g* | `find /data -group devs` |
| `find dir -perm 644` | Mode is **exactly** 644 | `find /etc -perm 644` |
| `find dir -perm -MODE` | **All** of these bits are set | `find /usr/bin -perm -4000` (setuid programs) |
| `find dir -perm /MODE` | **Any** of these bits is set | `find /data -perm /o=w` (writable by others) |
| `find ... -exec cmd {} \;` | Run *cmd* on every match | `find / -user harry -type f -exec cp -a {} /root/harry/ \; 2>/dev/null` |

### Key files

| File | Purpose |
|---|---|
| `/etc/login.defs` | `UMASK` (system default, `022`) and `HOME_MODE` (`0700`, permissions of new home directories) |
| `/etc/profile.d/*.sh` | Where a system-wide umask goes |
| `~/.bashrc` | Where a per-user umask goes |

### Exam notes

> - **Collaborative directory** ("members of `devs` share `/shared/devs`, new files belong to `devs`, nobody else gets in"): `mkdir -p /shared/devs; chgrp devs /shared/devs; chmod 2770 /shared/devs`. Verify `ls -ld /shared/devs` → `drwxrws---. root devs`, then `su - harry -c 'touch /shared/devs/t; ls -l /shared/devs/t'` → group is `devs`.
> - "...and users may delete only their own files" → add sticky: `chmod 3770`. Expect `drwxrws--T`.
> - **Test as the user, never as root.** root skips `r`/`w` checks, so a test as root proves nothing. Use `su - user -c 'cmd'`.
> - **Deleting a file depends on the directory**, not the file: `w` + `x` on the directory lets you delete even a read-only file you don't own (unless sticky is set).
> - **Owner bits win.** If harry owns a file, changing group or other bits never affects harry. Read the task: who is the owner?
> - `chmod -R 755 dir` makes every file executable. Use `chmod -R u=rwX,go=rX dir` instead.
> - **Numeric `chmod` keeps setgid on directories.** `chmod 770 /shared/devs` leaves the `s` in place. To remove it: `chmod g-s`. Always check with `ls -ld` afterwards.
> - In a setgid directory with umask `0022`, group members can **read** each other's files but not **edit** them. If the task says they must edit, they need umask `0002` or `chmod g+w` on the files. (Some course text written for RHEL 8 assumes `0002`.)
> - `ls -l` on a symlink always shows `lrwxrwxrwx`. Only the target's permissions matter: `ls -lL link` shows them.
> - **Reboot:** permissions and ownership live in the inode, so they persist. A `umask` typed at the prompt dies with the shell. Put it in `~/.bashrc` or `/etc/profile.d/`, then verify with `su - user -c umask`.

---

## Chapter 8 — Monitor and Manage Linux Processes

**Goal:** evaluate and control processes that run on a RHEL system.

| Course objective | Where in this chapter |
|---|---|
| Determine status, resource use and ownership of running programs, to control them | Process states · List processes · Status, resource use and ownership |
| Use Bash job control to manage several processes started from the same terminal session | Job control |
| Kill and communicate with processes, define the characteristics of a daemon, stop user sessions and processes | Signals · Send signals · Daemons · Stop user sessions and processes |
| Define load average and find resource-intensive server processes | Load average · `top` · Exam notes |

**EX200 objectives covered:** *identify CPU/memory-intensive processes and kill them*, and the basics of *adjust process scheduling* (nice/renice is finished in RH134).

### What a process is

A **process** is a running instance of a program. Each one has:

- a **PID** and a parent **PPID**
- an **owner** (UID/GID): decides what it can access and who may signal it
- its own memory, environment variables, open files, and a **state**

How processes are created (the same `fork()`/`exec()`/`wait()` as in C):

1. The parent calls `fork()`: a child copy with a new PID that inherits the environment, open files and owner.
2. The child calls `exec()`: it replaces itself with the new program.
3. The parent calls `wait()` to collect the child's exit status. Until it does, the dead child is a **zombie**.
4. If the parent dies first, **systemd (PID 1)** adopts the orphan and reaps it.

PID 1 is `systemd`, the ancestor of every user-space process. Names in `[brackets]` in `ps` output are kernel threads.

### Process states

| `ps` / `top` flag | Kernel name | Meaning |
|---|---|---|
| `R` | TASK_RUNNING | Running on a CPU, or waiting in the run queue |
| `S` | TASK_INTERRUPTIBLE | Sleeping until something happens (input, timer, signal). Most processes are here |
| `D` | TASK_UNINTERRUPTIBLE | Sleeping, usually waiting on disk or NFS I/O. **Ignores all signals, even `kill -9`** |
| `D` | TASK_KILLABLE | Like `D`, but a kill signal works. The course lists it as `K`; `ps` shows `D` |
| `I` | TASK_REPORT_IDLE | Idle kernel thread. Not counted in the load average |
| `T` | TASK_STOPPED | Stopped (`Ctrl+Z`, SIGSTOP, SIGTSTP). SIGCONT resumes it |
| `t` | TASK_TRACED | Stopped by a debugger |
| `Z` | EXIT_ZOMBIE | Finished; waiting for its parent to collect the exit status |
| `X` | EXIT_DEAD | Completely gone. You never see it |

Extra characters in the `STAT` column of `ps aux` / `ps lax`:

| Char | Meaning |
|---|---|
| `<` | High priority (negative nice value) |
| `N` | Low priority (positive nice value) |
| `s` | Session leader (usually a shell) |
| `l` | Multi-threaded |
| `+` | In the terminal's foreground process group |

### List processes: `ps` and `pstree`

| Command | What it does | Example |
|---|---|---|
| `ps` | Processes in **this** terminal only | `ps` |
| `ps aux` | All processes, user view (BSD syntax, **no dash**): USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND | `ps aux \| grep sshd` |
| `ps -ef` | All processes, full format (UNIX syntax): UID PID **PPID** C STIME TTY TIME CMD | `ps -ef \| less` |
| `ps lax` | Long format with numeric UIDs: adds PPID, PRI, NI, WCHAN. Fast (no name lookups) | `ps lax \| head` |
| `ps -u user` | One user's processes | `ps -u harry` |
| `ps -p PID` | One process (exit status 1 if it doesn't exist) | `ps -p 1234` |
| `ps -C name` | By exact command name | `ps -C sshd` |
| `ps -eo FIELDS` | Choose the columns yourself | `ps -eo pid,ppid,user,ni,stat,%cpu,%mem,cmd` |
| `--sort=-FIELD` | Sort; a leading `-` means descending | `ps aux --sort=-%cpu \| head -n 5` |
| `ps -ef --forest` / `ps axf` | Show parent/child relationships as a tree | `ps -ef --forest \| less` |
| `ps j` | Job-control columns: PPID, PGID, SID, TPGID | `ps j` |
| `pstree -p` | Process tree with PIDs (`psmisc` package) | `pstree -p harry` |

Two one-liners worth memorising:

```bash
ps -eo pid,user,%cpu,%mem,cmd --sort=-%cpu | head -n 6   # header + top 5 CPU users
ps -eo pid,user,%cpu,%mem,cmd --sort=-%mem | head -n 6   # header + top 5 memory users
```

### Status, resource use and ownership

| Question | Column (`ps`) | Command |
|---|---|---|
| What state is it in? | `STAT` (`S` in `top`) | `ps -o pid,stat,cmd -p PID` |
| How much CPU? | `%CPU`, `TIME` (total CPU time) | `ps -o pid,%cpu,time,cmd -p PID` |
| How much memory? | `%MEM`, `RSS` (= `RES` in `top`, KiB), `VSZ` (= `VIRT`) | `ps -o pid,%mem,rss,vsz,cmd -p PID` |
| Who owns it? | `USER` = **effective** user (whose permissions it uses) · `RUSER` = **real** user (who started it) | `ps -o pid,user,ruser,cmd -p PID` |
| Who started it? | `PPID` | `ps -o pid,ppid,cmd -p PID` |
| Which terminal? | `TTY` (`?` = none, typical for a daemon) | `ps -o pid,tty,cmd -p PID` |

- Effective and real user are normally the same. They differ for **setuid** programs: harry running `passwd` shows `USER root`, `RUSER harry` (chapter 7).
- `ps -u` and `pgrep -u` select by **effective** user; `ps -U` and `pgrep -U` select by **real** user.
- Ownership decides who can control a process: you can signal your own processes; root can signal all of them.

### Job control (inside one shell)

- A **job** is one command line or pipeline. Each job is its own **process group**.
- A terminal has **one foreground job** (it gets the keyboard and `Ctrl+C`) and any number of **background** jobs.
- Jobs belong to the shell that started them. Another terminal, `su`, or a new SSH session will not see them with `jobs`; use `ps`/`pgrep` there.
- A background job that tries to read from the keyboard is stopped automatically (SIGTTIN).

| Command / key | What it does | Example |
|---|---|---|
| `cmd &` | Start in the background; prints `[job number] PID` | `sleep 1000 &` → `[1] 5432` |
| `jobs` | List this shell's jobs (`+` = current, `-` = previous) | `jobs` |
| `jobs -l` | Same, with PIDs | `jobs -l` |
| `fg %N` | Bring job N to the foreground (`fg` alone = the `+` job) | `fg %1` |
| `Ctrl+Z` | Suspend the foreground job (SIGTSTP) → state `T` | |
| `bg %N` | Resume a stopped job in the background | `bg %1` |
| `Ctrl+C` | Interrupt the foreground job (SIGINT) | |
| `Ctrl+\` | Quit the foreground job (SIGQUIT, writes a core dump) | |
| `kill %N` | SIGTERM to job N | `kill %1` |
| `nohup cmd &` | Keep running after logout (ignores SIGHUP); output goes to `nohup.out` | `nohup ./backup.sh &` |

### Signals

| No. | Name | Default action | Sent by / used for |
|---|---|---|---|
| 1 | `SIGHUP` | Terminate | Terminal closed. Many daemons **reload their config** on HUP instead |
| 2 | `SIGINT` | Terminate | `Ctrl+C` |
| 3 | `SIGQUIT` | Terminate + core dump | `Ctrl+\` |
| 9 | `SIGKILL` | Terminate | **Cannot be caught, blocked or ignored.** Last resort: no cleanup |
| 15 | `SIGTERM` | Terminate | **Default for `kill`.** Polite: the program can clean up first |
| 18 | `SIGCONT` | Continue | Resume a stopped process |
| 19 | `SIGSTOP` | Stop | Pause. Cannot be caught or ignored |
| 20 | `SIGTSTP` | Stop | `Ctrl+Z`. Can be caught |

- The numbers are the x86_64 values and can differ on other hardware; the names are portable. `kill -9`, `kill -KILL` and `kill -SIGKILL` are the same.
- Escalation order the course recommends: **SIGTERM → SIGINT → SIGKILL**.
- A regular user can signal only **their own** processes. root can signal any process.

### Send signals: `kill`, `pgrep`/`pkill`, `killall`

| Command | What it does | Example |
|---|---|---|
| `kill -l` | List signal names and numbers | `kill -l`, `kill -l 15` → `TERM` |
| `kill PID` | SIGTERM to one or more PIDs | `kill 5432 5433` |
| `kill -SIGNAL PID` | Send a specific signal | `kill -9 5432`, `kill -SIGSTOP 5432` |
| `kill %N` | Signal a job of this shell | `kill -SIGKILL %2` |
| `pgrep pattern` | PIDs whose **name** matches. Use it as a dry run for `pkill` | `pgrep -l sshd` |
| `pgrep -a pattern` | PID + full command line | `pgrep -a sha1sum` |
| `pkill pattern` | Signal every process whose name matches | `pkill sha1sum` |
| `pkill -x name` | **Exact** name only | `pkill -x sleep` |
| `pkill -f text` | Match against the **full command line** | `pkill -f backup.sh` |
| `pkill -u user` | All processes of a user | `pkill -SIGKILL -u bob` |
| `pkill -t TTY` | All processes on one terminal | `pkill -SIGKILL -t pts/1` |
| `pkill -P PPID` | Children of one parent (the parent survives) | `pkill -SIGKILL -P 8391` |
| `killall name` | Signal by **exact** command name (`psmisc` package) | `killall -u bob sleep` |

`pgrep` and `pkill` take the same selection options (`-u`, `-t`, `-P`, `-x`, `-f`), so test with `pgrep` and then swap the command name.

Signals are also how you **communicate** with a process, not only how you end it: SIGSTOP/SIGCONT pause and resume it, and SIGHUP tells many daemons to re-read their config without restarting.

### Daemons

A **daemon** is a long-running background process that provides a service.

| Characteristic | How you see it |
|---|---|
| Started automatically at boot (by systemd); runs until shutdown or until stopped | `systemctl status sshd` |
| No controlling terminal | `TTY` is `?` in `ps -ef` |
| Parent is systemd | `PPID` is `1` |
| Name usually ends in `d` | `sshd`, `chronyd`, `crond`, `rsyslogd`, `httpd` |
| Often runs as a dedicated system user (UID < 1000) to limit damage if compromised | `ps -o user,comm -C chronyd` → `chrony` |
| Sleeps (`S`) until a request or event arrives | `ps -o stat,comm -C sshd` |
| Writes to the journal or log files, never to a screen | `journalctl -u sshd` (chapter 11) |
| Often reloads its config on SIGHUP | `kill -HUP PID`; for services prefer `systemctl reload` (chapter 9) |

```bash
ps -eo pid,ppid,user,tty,comm | awk 'NR == 1 || ($2 == 1 && $4 == "?")'   # children of systemd with no terminal
ps -o pid,ppid,user,tty,stat,comm -C sshd                                # one daemon's details
```

**RHEL 9 vs 10:** on RHEL 10 (OpenSSH 9.8+), the `sshd` daemon only listens; each login runs in its own `sshd-session` process. `pgrep sshd` matches both, `pgrep -x sshd` only the daemon.

### Stop user sessions and processes

| Goal | Command |
|---|---|
| Who is logged in, on which terminal (TTY), idle time, current command | `w` |
| bob's processes / process tree | `pgrep -l -u bob`, `pstree -p bob` |
| Log bob out and kill **everything** he owns | `pkill -SIGKILL -u bob` |
| Kill one of bob's sessions only | `pkill -SIGKILL -t pts/1` |
| Kill bob's programs but keep his login shell | `pkill -SIGKILL -P <PID of his shell>` |
| List login sessions (ID, user, TTY) | `loginctl list-sessions` |
| End one session by ID | `loginctl terminate-session <ID>` |
| End all of bob's login sessions | `loginctl terminate-user bob` |

`pkill -u bob` hits every process bob owns, including ones a service runs as bob. `loginctl` only ends his login sessions and what they started. The course method is `pkill -SIGKILL -u bob`.

### Monitor activity: load average

The **load average** is the average number of processes that are **runnable (R)** or **waiting for I/O (D)**, over the last **1, 5 and 15 minutes**. On Linux it includes I/O waits, not only CPU demand.

| Command | What it shows | Example output |
|---|---|---|
| `uptime` | Time since boot, logged-in users, load averages | `load average: 2.92, 4.48, 5.20` |
| `cat /proc/loadavg` | The same 3 values + running/total tasks + last PID | `2.92 4.48 5.20 3/412 9921` |
| `lscpu` | CPU details; read the `CPU(s):` line | `lscpu \| grep '^CPU(s)'` |
| `nproc` | Just the number of CPUs | `nproc` → `4` |
| `free -h` | RAM and swap summary | `free -h` |

**Divide the load by the number of CPUs:**

| Load on a 4-CPU system | Per CPU | Meaning |
|---|---|---|
| `2.00` | 0.50 | Half the capacity in use, nothing waits |
| `4.00` | 1.00 | Fully used, nothing waits yet |
| `8.00` | 2.00 | All CPUs busy, about 4 processes waiting at any moment |

- 1-minute value above the 15-minute value → load is rising. Below it → load is falling.
- High load with low CPU usage and high `wa` in `top` → an I/O bottleneck (processes in `D`), not a CPU problem.

### Monitor activity: `top`

**Header lines**

| Line | Shows |
|---|---|
| `top - 10:02:11 up 3:12, 2 users, load average: ...` | Same as `uptime` |
| `Tasks: total, running, sleeping, stopped, zombie` | Process count per state |
| `%Cpu(s): us sy ni id wa hi si st` | user, system, niced, **idle**, **I/O wait**, hardware/software interrupts, stolen by the hypervisor |
| `MiB Mem` / `MiB Swap` | RAM and swap: total, free, used, buff/cache, available |

**Columns**

| Column | Meaning |
|---|---|
| `PID`, `USER` | Process ID, owner |
| `PR`, `NI` | Kernel priority, nice value |
| `VIRT` | All memory the process has mapped (including shared libraries and memory not yet used) |
| `RES` | Physical RAM actually in use: **the real memory footprint** |
| `SHR` | The part of RES shared with other processes |
| `S` | State (R, S, D, T, Z, I) |
| `%CPU` | CPU use since the last refresh. Above 100% = a multi-threaded process using several CPUs |
| `%MEM` | RES as a percentage of total RAM |
| `TIME+` | Total CPU time used since the process started |
| `COMMAND` | Program name (`c` shows the full command line) |

**Interactive keys** (case matters)

| Key | Action |
|---|---|
| `?` or `h` | Help |
| `P` (Shift+P) | Sort by %CPU (the default) |
| `M` (Shift+M) | Sort by %MEM |
| `T` (Shift+T) | Sort by TIME+ |
| `k` | **Kill**: asks for a PID (defaults to the top row), then a signal (default 15) |
| `r` | **Renice**: asks for a PID, then a nice value |
| `u` | Show one user's processes only |
| `c` | Full command line on/off |
| `1` | One line per CPU |
| `l` / `t` / `m` | Toggle the load / tasks + CPU / memory header lines |
| `H` (Shift+H) | Show threads instead of processes |
| `s` or `d` | Change the refresh interval (seconds) |
| `f` | Choose and order columns |
| `W` (Shift+W) | Save the current layout to `~/.config/procps/toprc` |
| `q` | Quit |

**Command-line options**

| Command | What it does |
|---|---|
| `top -u user` | Only one user's processes |
| `top -p PID,PID` | Only these PIDs |
| `top -o %MEM` | Start sorted by memory |
| `top -d 2` | Refresh every 2 seconds |
| `top -b -n 1 \| head -n 15` | **Batch mode**, one snapshot: for scripts or saving output to a file |

### Process priority: `nice` and `renice` (basics; RH134 covers the rest)

- Nice range: **-20** (highest priority) to **19** (lowest). Default: **0**.
- Regular users can only **raise** the nice value of their own processes (lower priority). Only root can lower it.

| Command | What it does | Example |
|---|---|---|
| `nice -n N cmd` | Start a command with nice N (`nice cmd` alone = 10) | `nice -n 15 tar czf /tmp/etc.tgz /etc &` |
| `renice -n N -p PID` | Change a running process | `renice -n 5 -p 5432` → `old priority 0, new priority 5` |
| `ps -o pid,ni,comm -p PID` | Check the nice value (or the `NI` column in `top`) | `ps -o pid,ni,comm -p 5432` |

### Key files

| File | Purpose |
|---|---|
| `/proc/` | Virtual file system: the kernel's live view of processes and hardware. Nothing is on disk; it is rebuilt at every boot |
| `/proc/PID/` | One directory per running process |
| `/proc/PID/status` | Name, `State:`, `PPid:`, `Uid:`, memory (`VmRSS:`) in readable form |
| `/proc/PID/cmdline` | Full command line, NUL-separated: `tr '\0' ' ' < /proc/PID/cmdline` |
| `/proc/PID/fd/` | Open file descriptors (symlinks to the open files) |
| `/proc/loadavg` | Load averages, running/total tasks, last PID used |
| `/proc/cpuinfo` | CPU details (`grep -c ^processor /proc/cpuinfo` = number of CPUs) |
| `/proc/meminfo` | Memory details (what `free` and `top` read) |
| `~/.config/procps/toprc` | `top` settings saved with `W` |

### Finding help offline

| Need | Where |
|---|---|
| State codes, column names for `-o` | `man ps` → `/PROCESS STATE CODES`, `/STANDARD FORMAT SPECIFIERS`, `/EXAMPLES` |
| Signal names, numbers, default actions | `kill -l`; `man 7 signal` → `/Standard signals` |
| `pgrep`/`pkill` options | `man pgrep` (one page covers both) |
| `top` keys | `?` inside `top`; `man top` → `/INTERACTIVE` |
| `jobs`, `fg`, `bg` | `help jobs`, `help fg`, `help bg`; `man bash` → `/^JOB CONTROL` |
| `/proc` files | `man 5 proc` |

### Exam notes

> - **"Find the process using the most CPU and terminate it":** run `top` (already sorted by %CPU), note the PID, press `k` (or `kill PID` in a shell). Verify: `ps -p PID` shows only the header line. Trust `top` over `ps aux` here: `ps` `%CPU` is the **average over the process's whole lifetime**, so a hog that started a minute ago can rank lower in `ps` than it really is.
> - **It came back with a new PID?** Something restarts it: a systemd service or a cron/timer job. `systemctl status PID` shows which unit owns the process → `systemctl disable --now <unit>` (chapter 9). Killing a process is never permanent; if the task says it must not run after a reboot, you have to deal with the unit.
> - **Escalate gently:** `kill PID`, check with `ps -p PID`, and only then `kill -9 PID`. SIGKILL skips cleanup (temp files, lock files, half-written data).
> - **`kill %1` is not `kill 1`.** `%1` is your job; `1` is PID 1, systemd.
> - **A stopped process (`T`) ignores `kill PID` until it continues.** The SIGTERM waits in the queue. Use `kill -9 PID`, or `kill PID` followed by `kill -CONT PID`. Bash's `kill %N` on a stopped job sends the SIGCONT for you.
> - **`pkill` matches part of the name.** `pkill sleep` also kills `sleepy`. Run the same pattern with `pgrep -l` first, use `-x` for an exact name, and `-u` to limit it to one user.
> - **Process names are cut to 15 characters.** For longer names `pgrep`/`pkill` match nothing (pgrep prints a warning); use `-f`.
> - **Logging a user out needs SIGKILL.** An interactive bash ignores SIGTERM, so `pkill -u bob` can leave him logged in. Use `pkill -SIGKILL -u bob`; verify with `w` and `pgrep -u bob` (no output).
> - **`D` and `Z` processes can't be killed.** A `D` process waits on I/O (fix the disk or NFS server). A zombie is already dead: find its parent with `ps -o ppid= -p <zombie PID>` and kill or restart that.
> - **"Save X to a file" tasks:** `pgrep -u harry > /root/harry-pids.txt`, or `ps -eo pid,user,%mem,cmd --sort=-%mem | head -n 6 > /root/top-mem.txt`. Verify with `cat`. Files in `/root` survive a reboot; the processes don't.
> - **Reboot:** nothing in this chapter persists. Processes, jobs, nice values and `/proc` all start fresh. Anything that must survive a reboot belongs in a systemd unit (chapter 9) or tuned/`Nice=` (RH134).
> - **RHEL 9 vs 10:** same commands and options. RHEL 10 ships procps-ng 4.x, which changes nothing you type in this chapter.

### Exam-speed hacks

> - **Who owns this PID?** `systemctl status 5432` names the service (or user session scope) a process belongs to. `ps -o pid,unit,cmd -p 5432` shows the same in one line.
> - **Dry run, then kill, without retyping:** run `pgrep -a -u bob sleep`, check the list, then type `^pgrep -a^pkill^`. Bash re-runs the previous command with the first `pgrep -a` replaced by `pkill`.
> - **No man page needed for signals:** `kill -l` lists every name and number; `kill -l 19` → `STOP`.

### Try it on servera (about 25 minutes)

1. Start three CPU hogs: `sha1sum /dev/zero &` three times. Check them with `jobs -l`.
2. Run `uptime` and `nproc`, wait a minute, run `uptime` again. Work out the load per CPU.
3. In `top`: find the hogs, `r` one of them to nice 10, `k` another with the default signal. Quit and confirm with `pgrep -a sha1sum`.
4. `fg` the last job, `Ctrl+Z`, check its state with `ps -o pid,stat,cmd -p PID`, then `kill PID` and see that it is still there in `T`. Finish it with `kill -CONT PID`.
5. SSH into servera as harry in a second terminal and run `sleep 1000 &`. As root: `w`, `pgrep -l -u harry`, `pkill -u harry` (does harry stay logged in?), then `pkill -SIGKILL -u harry`. Confirm with `w`.
6. Find which service owns a process: `pgrep -x chronyd`, then `systemctl status <that PID>` and `ps -o pid,unit,cmd -p <that PID>`. This is the first thing to check when a process you killed comes back.
7. Spot the daemons with the `awk` one-liner from the Daemons section. For three of them, write down the user they run as and whether their name ends in `d`.
8. Run `passwd` as harry in one terminal and leave it at the prompt. As root in another: `ps -o pid,user,ruser,cmd -C passwd`. Explain why USER and RUSER differ.

---

<!--
TEMPLATE — copy this block for each new chapter, then fill it in.

## Chapter N — Title

### Commands

| Command | What it does | Example |
|---|---|---|
| `cmd` | ... | `cmd -x arg` |

### Key files

| File | Purpose |
|---|---|
| `/etc/...` | ... |

### Exam notes

> - Trap / how to verify / does it survive a reboot?
-->
