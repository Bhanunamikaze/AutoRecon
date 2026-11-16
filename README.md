# AutoRecon

Automate Nmap Scanning and Recon Activities for Common Ports & Services
  
## Summary

The main goal for this script is to automate the process of enumeration & recon that is run every time, and instead focus our attention on real pentesting.  
  
This will ensure two things:  
1. Automate nmap scans. 
2. Always have some recon running in the background. 

Once initial ports are found '*in 5-10 seconds*', we can start manually looking into those ports, and let the rest run in the background with no interaction from our side whatsoever.  

## Features

### Scans
1. **Network** : Shows all live hosts in the host's network (~15 seconds)
2. **Port**    : Shows all open ports (~15 seconds)
3. **Script**  : Runs a script scan on found ports (~5 minutes)
4. **Full**    : Runs a full range port scan, then runs a thorough scan on new ports (~5-10 minutes)
5. **UDP**     : Runs a UDP scan "requires sudo" (~5 minutes)
6. **Vulns**   : Runs CVE scan and nmap Vulns scan on all found ports (~5-15 minutes)
7. **Recon**   : Suggests recon commands, then prompts to automatically run them
8. **All**     : Runs all the scans (~20-30 minutes)

*Note: This is a reconnaissance tool, and it does not perform any exploitation.*

## Usage:
```
./AutoRecon.sh -h
Usage: AutoRecon.sh -H/--host <TARGET-IP> -t/--type <TYPE>
Optional: [-r/--remote <REMOTE MODE>] [-d/--dns <DNS SERVER>] [-o/--output <OUTPUT DIRECTORY>] [-s/--static-nmap <STATIC NMAP PATH>] [-e/--env-file <.ENV FILE OR DIR>]

Scan Types:
	Network : Shows all live hosts in the host's network (~15 seconds)
	Port    : Shows all open ports (~15 seconds)
	Script  : Runs a script scan on found ports (~5 minutes)
	Full    : Runs a full range port scan, then runs a thorough scan on new ports (~5-10 minutes)
	UDP     : Runs a UDP scan "requires sudo" (~5 minutes)
	Vulns   : Runs CVE scan and nmap Vulns scan on all found ports (~5-15 minutes)
	Recon   : Suggests recon commands, then prompts to automatically run them
	All     : Runs all the scans (~20-30 minutes)
```

**Example scans**:
```
./AutoRecon.sh --host 10.1.1.1 --type All
./AutoRecon.sh -H 10.1.1.1 -t Basic
./AutoRecon.sh -H academy.htb -t Recon -d 1.1.1.1
./AutoRecon.sh -H 10.10.10.10 -t network -s ./nmap
```

**Simple Way to Batch Scan All the IPs**
```
#Create a new file called /usr/local/bin/startscan.sh
if [ $# -eq 0 ]
        then
                echo "Usage: $0 filename.txt"
                echo "Example: $0 ips.txt"
                exit
        else
                "$1"
fi

# Absolute path to the .env file (or its directory) holding NVD_API_KEY - optional 2nd argument overrides it
ENV_FILE="${2:-/opt/autorecon/.env}"

for ip in $(cat $1); do AutoRecon.sh -H $ip -t All -e "$ENV_FILE" & done
```

## CVE Lookup

The Recon step runs `cveLookup` on every port where nmap identified a product and version. It takes nmap's CPE (from the XML output, `nmap/*.xml`) and queries the [NVD CVE API](https://nvd.nist.gov/developers/vulnerabilities) with `virtualMatchString`, so version ranges such as "2.4.0 through 2.4.55" are matched. This finds far more than `vulners.nse`. If nmap gave no CPE, it guesses one from the NVD CPE dictionary and labels it as a guess. CVEs on CISA's Known Exploited Vulnerabilities list are flagged `KEV` and listed first, and `searchsploit` results are appended when it is installed. Output goes to `recon/cve_<host>_<port>_<proto>.txt`.

- Requires `curl` and `jq`.
- Without an API key NVD allows 5 requests per 30 seconds, so each lookup waits 6 seconds. A free key ([request one here](https://nvd.nist.gov/developers/request-an-api-key)) raises the limit to 50 requests:
  ```
  cp .env.sample .env      # then set NVD_API_KEY=<your key> in .env
  ```
  To keep the key in one fixed place, give an absolute path to the file or to the directory that contains it:
  ```
  ./AutoRecon.sh -H 10.10.10.10 -t Recon -e /opt/secrets/autorecon/.env      # per run
  export AUTORECON_ENV_FILE=/opt/secrets/autorecon                           # permanent (add to ~/.bashrc)
  ```
  Lookup order (the first file that sets a variable wins, and variables already exported in your shell beat all files):
  1. `./.env` in the current working directory
  2. `-e/--env-file <path>`, or `$AUTORECON_ENV_FILE` if no flag is given
  3. `.env` in the `AutoRecon.sh` directory

  The file is parsed, not executed, and `.gitignore` keeps `.env` out of git. If NVD rejects the key, the lookup says so and continues without it.
- Ubuntu, Debian and RHEL backport fixes without changing the upstream version, so the output warns that some of the listed CVEs may already be patched.

## Auto Recon Services List 
- FTP
- SSH
- HTTP/SSL 
- CMS (Wordpress, Joomla, Drupal)
- Kerberos
- LDAP / LDAPS / FreeIPA
- MSSQL
- MySQL
- PostgreSQL
- MongoDB
- Cassandra
- CouchDB
- ORACLE DB
- SNMP
- SMTP
- NTP 
- NFS
- RPC/RPC MAPPER
- SMB
- Rsync
- R-Services (rlogin/rwho/rusers)
- JAVA RMI SERVICES
- KUBERNETES
- Docker API
- ACTIVEMQ
- WinRM
- RDP
- Redis
- Memcached
- Elasticsearch
- Grafana
- Jenkins
- Jolokia
- Apache Jserv (AJP)
- IPMI
- VMware ESXi
- VNC

## Recon Tools Used
 dirsearch gobuster nikto testssl.sh ffuf sslscan joomscan wpscan python python3-pip python3 ldap-utils git smbmap smbclient snmpwalk snmpbulkwalk enum4linux onesixtyone snmp odat tnscmd10g default-jre nuclei droopescan cottontail ldeep Kube Hunter etcdctl BeanShooter BaRMIe Remote-method-guesser ssh-audit kerbrute evil-winrm redis-tools impacket rsync memcached psql rpcclient rpcinfo ipmitool curl jq mongosh cqlsh xfreerdp rdp-sec-check ajpShooter [VMwareAPIPentest](https://github.com/Bhanunamikaze/VMwareAPIPentest) [ESXiBrute](https://github.com/Bhanunamikaze/ESXiBrute) [SMBScan.sh](https://github.com/Bhanunamikaze/VulnFinder/blob/main/SMBPentest.sh)

