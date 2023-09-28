# CHEATSHEET FOR OSSA COURSE 

## Things to take note 
- 

## Part 1: What is Information Security

```
no commands to take note of
```

## Part 2: Defending Your Turf & Securing Policy Formulation

## Part 3: Network 101

## Part 4: Defence Tools & Lockdown 

### Firewall

- fwbuilder is in Centos7 OS

### NIDS (Snort)

- Check Snort Configuration File
`look out for HOME_NET for ip address range to be monitored`
```
nano /etc/snort/snort.conf
```
- Starting Snort
```
snort -c /etc/snort/snort.conf &
```
- Starting Snort (with alert onscreen)
```
snort -A console -c /etc/snort/snort.conf &
```
- View Alert Logs
`-f keeps tail open and see live updates to file`
```
tail -f /var/log/snort/alert
```

### HIDS (Tripwire)

- Prepare tripwire config file
```
tripwire-setup-keyfiles
```
- Take a snapshot of filesystem using Tripwire
```
tripwire --init
```
- Check any changes to filesystem
```
tripwire --check
```
- View tripwire report
`<filename> is hostname of server and <timestamp> is timestamp file is created`
```
twprint -m r --twrfile /var/lib/tripwire/report/<filename>-<timestamp>.twr
```
- Policies can be edited on the Tripwire Policy text file: `twpol.txt`

- Update the Tripwire database whenever `twpol.txt` was edited
```
tripwire --update --twrfile /var/lib/tripwire/report/<filename>
```

### GPG Encryption & Verification

- Generate new GPG keypair
```
gpg --gen-key
```
- Import Public Key
`<filename> is name of file containing public key of 3rd party to import`
```
gpg --import <filename>
```
- View imported keys
`<keyid> is the string of number after /`
```
gpg --fingerprint
```
- Sign imported key to verify source of public key
```
gpg --sign-key <keyid>
```
- Verify authenticity of files sign by 3rd party private key
`<filename1> is public signature by originator of file & <filename2> is file whose integrity to be verified`
```
gpg --verify <filename1> <filename2>
```

## Part 5: The 5E Attacker Methodology

### Exploration 
**DNS Reconnaissance**

- Find IP address of server
```
dig securitystartshere.org
```
**dig options:**
- `mx` - find **mail exchanger** records
- `ns` - find **name server** records 
- `soa` - find **SOA (Start of Authority)** 
	- stores information about the name of the server, zone administrator, current version of zone data file etc.

- Perform a zone transfer of the pod's DNS internal server
```
dig @10.50.8.1 pod8.com axfr
```

**Whois Reconnaissance**

- Find where domain is registered
```
whois securitystartshere.org
```

### Enumeration  
**Port Scanning**

- Ping Sweep to determine how many host(s) in subnet
```
nmap -sP -n 10.50.8.0/24
```
- 

### Exploitation

### Embedding

### Egress

## Part 6: Wireless Insecurity

## Part 7: Incident Response & Computer Forensics

## Part 8: The Impact of Law