+++
title = "Enumeration"
date = "2019-11-17"
categories = [
    "security",
]

+++

### Subdomain Discovery

##### Subdomain with amass

```bash
amass enum -active -d hackerone.com -max-depth 3 -config amass.config.ini
```

Example of config file https://github.com/OWASP/Amass/blob/master/examples/config.ini

Others: [subfinder](https://github.com/projectdiscovery/subfinder), [findomain](https://github.com/Edu4rdSHL/findomain/), [assetfinder](https://github.com/tomnomnom/assetfinder)



### Directory brute-force

##### Directory brute-force with gobuster

```bash
gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u http://jeff.thm/backups/ -t 20 -x zip,txt,log,db,json,js,conf
```

Wordlist: Fuzzing/fuzz-Bo0oM.txt, Web-Content/common.txt

##### Directory brute-force with wfuzz

```bash
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/big.txt -z list,php-txt-zip-log-db-json --hc 403,404 -t 20 -u http://10.10.10.187/utility-scripts/FUZZ.FUZ2Z
```
