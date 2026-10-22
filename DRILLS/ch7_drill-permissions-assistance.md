# Drill: Permissions (easy), assistance log

**Date:** 2026-10-22
**Drill file:** `2026-10-22_drill-permissions.md`

## Summary

| Task | Topic | Help needed |
|---|---|---|
| 1 | Owner and group of a file (`chown`) | None |
| 2 | File permissions (`chmod`) | None |
| 3 | Execute permission on a script | None |
| 4 | Account expiry (`chage -E`) | None (asked a question, see below) |
| 5 | Directory group + permissions | None (asked a question, see below) |
| 6 | setgid on a shared directory | **Nudge** |
| 7 | Sticky bit | None |
| 8 | Persistent umask for a user | **Hint + full solution** |

---

## Task 6: setgid on a shared directory

- **First attempt:** `chown -R :sales /perms/salesdocs`
- **Why it wasn't enough:** it changes the group of the files that exist now. Files created later still get the creator's primary group.
- **Nudge:** the fix is a special permission on the directory itself (`man chmod`, search `/SETUID`).
- **Solved after the nudge:** `chmod g+s /perms/salesdocs`
- **Takeaway:** "new files in this directory must belong to group X" → setgid on the directory. `ls -ld` shows it as `rws` in the group part. New subdirectories inherit it.

---

## Task 8: persistent umask for victor

- **Hint needed:** I thought directories needed a separate setting from files. They don't: one umask covers both. Only the starting point differs (files 666, directories 777).
- **Calculation:** got `027` right (start from 777 for a directory target, remove `w` from group and `rwx` from others).
- **Mistake:** wrote `umask=027` in `/home/victor/.bashrc`. That creates a variable called `umask`; it doesn't change the umask.
- **Full solution:** the line must be `umask 027` (space, no `=`).
- **Repeated mistake:** second drill in a row with `=` in a umask line (previous drill, 2026-10-20: `UMASK=137`).
- **Takeaway:** `umask 027` is a command, the same in the file as at the prompt. Test with `su - victor`, which reads `.bashrc` fresh. `source` isn't needed and, when run as root, only changes root's shell.

---

## Questions asked (not assistance)

- **Task 4:** `chage -l paul` showed Dec 30 instead of Dec 31. `/etc/shadow` field 8 stores days since 1970-01-01 in UTC, and the time zone can shift the conversion by a day. Check the stored number: 20818 = 2026-12-31.
- **Task 5:** `m1` was `maria:maria`, not `maria:sales`. New files get the creator's **primary** group. The directory's group is ignored unless setgid is set (that's task 6).
- **`source`:** runs a file's commands in the current shell. `.bashrc` is read automatically when a new shell starts, so editing the file is enough for future logins.

---

## Next focus

- umask: syntax (`umask NNN`) and calculating from the directory target.
- setgid: recognizing the "new files must belong to group X" wording.
