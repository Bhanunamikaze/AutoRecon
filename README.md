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
Optional: [-r/--remote <REMOTE MODE>] [-d/--dns <DNS SERVER>] [-o/--output <OUTPUT DIRECTORY>] [-s/--static-nmap <STATIC NMAP PATH>]

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

for ip in $(cat $1); do AutoRecon.sh -H $ip -t All & done
```

## Auto Recon Services List 
- HTTP/SSL 
- CMS (Wordpress, Joomla, Drupal)
- LDAP
- ORACLE DB
- SNMP
- SMTP
- NTP 
- NFS
- RPC/RPC MAPPER
- SMB
- JAVA RMI SERVICES
- KUBERNETES
- ACTIVEMQ

## Recon Toools Used
 dirsearch gobuster nikto testssl.sh ffuf sslscan joomscan wpscan python python3-pip python3 ldap-utils git smbmap smbclient snmpwalk enum4linux onesixtyone snmp odat tnscmd10g default-jre nuclei droopescan cottontail ldeep Kube Hunter etcdctl BeanShooter BaRMIe Remote-method-guesser [SMBScan.sh](https://github.com/Bhanunamikaze/VulnFinder/blob/main/SMBPentest.sh)

## TO-DO List
- [x] Script to Verify and Download Tools
- [ ] Pending Recon Tools
	- [x] ActiveMQ
	- [ ] Nuclei
	- [ ] Jolokia
	- [x] RMI
	- [x] RPC Mapper
	- [x] Kubernetes
	- [x] NTP
	- [x] NFS
	- [ ] Apache Jserv
- [ ] Create a Set of Common Endpoints to Detect Web Services
- [x] Update LDAPSearch with extra commands to make it work with all versions

