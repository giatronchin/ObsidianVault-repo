#hacking #api


**Nmap Scans**
```bash
sudo nmap -sC -sV 127.0.0.1   #default script (-sC), surface enumaration(-sV) 
sudo nmap -p- 127.0.0.1       #All ports scan (-p-)
sudo nmap -sV 127.0.0.1 -p 8025 #Surface enumaration on specific port number
```

**OWASP Amass**
Can perform either **passive**(_-passive_, collect from other external sources like hakerone, shodan) or **active**(_-active_ directly from the target) reconnaissance collecting OSINT.
```bash
amass enum -list #service for external OSINT sources

amass enum -active -d crapi.apisec.ai #target domain (-d)
amass enum -active -d microsoft.com | grep api

```
**Gobuster**
Directory enumeration
```bash
gobuster dir -u URL_target -w /path/wordlist #Brute-force domain enumeration 

gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# -b or -status-codes-blacklist string     Negative status codes (will override status-codes if set) (default "404")
gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -b 200 #Filters out output http with response code equal to 200




```