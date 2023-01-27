+++
title = "Enumeration"
date = "2019-11-17"
categories = [
    "security",
]

+++

## Subdomain Discovery

### Subdomain with amass

```bash
amass enum -active -d hackerone.com -max-depth 3 -config amass.config.ini
```

Example of config file https://github.com/OWASP/Amass/blob/master/examples/config.ini

Others: [subfinder](https://github.com/projectdiscovery/subfinder), [findomain](https://github.com/Edu4rdSHL/findomain/), [assetfinder](https://github.com/tomnomnom/assetfinder)



## Directory brute-force

Wordlist: assetnote, Fuzzing/fuzz-Bo0oM.txt, Web-Content/common.txt, Web-Content/raft-large-directories.txt

### Directory brute-force with ffuf
```bash
ffuf -u http://118.70.116.235:8052/current_config/FUZZ -w ~/Downloads/assetnote/data/manual/raft-large-directories-lowercase.txt -ac -mc 200,204,301,307,400,401,403,405 -t 10 -e .js,.conf
```
### Directory brute-force with gobuster

```bash
gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u http://hackerone.com/backups/ -t 20 -x zip,txt,log,db,json,js,conf --status-codes-blacklist
```

### Directory brute-force with wfuzz

```bash
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/big.txt -z list,php-txt-zip-log-db-json --hc 403,404 -t 20 -u http://10.10.10.187/utility-scripts/FUZZ.FUZ2Z
```

`--hc/hl/hw/hh`: Hide responses with the specified code/lines/words/chars

`-c`: Output with colors


## Parameter brute-force
### Parameter brute-force with x8
```bash
x8 -u https://hackerone.com/reset -w ~/Downloads/reindeer/wordlists/param_adu_vjp -X POST -t json
```

`-t`: type (urlencode, json)
