# Investigate Linux

## Users and Shells
Current logged users
```
    # w
    # who
    # netstat -nalp | grep ":22"
```

Last logged users
```
    # last
```

Look for full pathnames of valid login shells
```
    # cat /etc/shells
```

## Unusual Accounts
Look in /etc/passwd for new accounts in sorted list by UID:
```
    # sort -nk3 -t: /etc/passwd | less
```

Normal accounts will be there, but look for new, unexpected accounts, especially with UID < 500

Also, look for unexpected UID 0 accounts:
```
    # egrep ':0+:' /etc/passwd
```

On systems that use multiple authentication methods:
```
    getent passwd | egrep ':0+:'
```

Look for orphaned files, which could be a sign of an attacker's temporary account that has been deleted.
```
    # find / -nouser -print
```

## Network
Look for promisous mode, which might indicate a sniffer:
```
    ip link | grep PROMISC
```

Look for unusual port listeners
```
    # netstat -nap
```

Look for all network connects in system
```
    # netstat -lntup
```

Get more details about running processes listening on ports:
```
    lsof -i
```

Look for unsual ARP entries, mapping IP address to MAC address that aren't correct for the LAN:
```
    # arp -a
```

Monitor network traffic
```
    # iftop
```

## Command history
View bash history 
```
    # history
    # cat /home/USER_YOU_WANT_TO_VIEW/.bash_history
```

View user's sudo history
```
    # tail /var/log/auth.log | grep username        # On Debian-based
    # tail /var/log/secure | grep username          # On RHEL-based
```

## Files
Look for files in the Common Attack Points
```
    # ls -la /tmp
    # ls -la /var/tmp
```

Look for unusual SUID root files:
```
    # find / -uid 0 -perm -400 -print
```
This requires knowledge of normal SUID files.

Look for unusual large files (greater than 10 MegaBytes):
```
    # find / -size +10000k -print
```

This requires knowledge of normal large files.

Look for files named with dots and spaces ("...", "..", ". ", and " ") used to camouflage files:
```
    # find / -name " " -print
    # find / -name ".. " -print
    # find / -name ". " -print
    # find / -name " " -print
```

Look for processes running out of or accessing files that have been unlinked (i.e, link count is zero). An attacker may be hiding data in or running a backdoor from such files:
```
    # lsof +L1
```

Look for recently modified files
```
    # find / -mtime -2      # find only files that was modified less than 2 Days
```
atime: access tie or Last access time
mtime: modify time or Last modification time
ctime: change tme or Last change time


## Processes and Services
Look at all running processes:
```
    # ps aux
    # ps -ef
    # pstree
    # ps -ejH
    # top
    # htop
```

Get familiar with "normal" processes for the machine. Look for unsual process. Focus on processes with root (UID 0) privileges.

If you spot a process that is unfamiliar, investigate in more detail using:
```
    # lsof -p [pid]
```

This command shows all files and ports used by the running process

If your machine has it installed, run chkconfig to see which services are enabled at various runlevels:
```
    # chkconfig --list
```


## Scheduled Tasks
Look for cron jobs scheduled by root and any other UID 0 accounts:
```
    # crontab -u root -l
```

Look for cron jobs scheduled by all users:
```
    # for user in $(cut -f1 -d: /etc/passwd); do echo $user; crontab -u $user -l; done
```

Look for unusual system-wide cron jobs:
```
    # cat /etc/crontab
    # ls /etc/cron.*
```

## Binary
Look for hash of binary
```
    # md5sum [file]
    # sha1sum [file]
    # sha256sum [file]
```

Look for information of object files
```
    # objdump -f [file]
    # objdump -d [file]
```

## Referrences
[Sans](https://sansorg.egnyte.com/dl/ejj5SM2pjA)
[Don't You Know Joe](https://sites.google.com/site/zenarstudio/home/kb/linux---howto---investigate-a-linux-compromise)
[Alexnogard](https://alexnogard.com/investigate-if-your-linux-server-is-hacked-or-not/)