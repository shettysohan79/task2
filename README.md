Step 1: Passive Reconnaissance
Gather publicly available domain registration and DNS record details without alerting the target infrastructure:
whois google.com
nslookup google.com

Step 2: Active Reconnaissance &amp; Subnet Sweep
Discover live hosts within the isolated VirtualBox host-only subnet using ICMP echo / ARP discovery:
nmap -sn 192.168.56.0/24

Step 3: Port, Service &amp; OS Fingerprinting
Perform structured port auditing against target 192.168.56.101 across TCP and UDP, saving scan reports:

# Full TCP SYN Stealth Scan
nmap -sS -p- 192.168.56.101 -oN tcp_scan.txt
# Top 100 UDP Port Scan
sudo nmap -sU --top-ports 100 192.168.56.101 -oN udp_scan.txt
# Service Version Detection
nmap -sV 192.168.56.101 -oN service_scan.txt
# OS Detection
sudo nmap -O 192.168.56.101 -oN os_scan.txt
# Consolidated Full Assessment (Service Version + OS + All Ports)
sudo nmap -sV -O -p- 192.168.56.101 -oA full_scan_report

Step 4: Inspecting Scan Results
Review comprehensive scan results formatted in standard Nmap text output:
cat full_scan_report.nmap

Step 5: Vulnerability Assessment with OpenVAS / GVM
Deploy and run the Greenbone Vulnerability Management (GVM) automated scanner:
sudo apt install gvm -y
sudo gvm-setup
sudo gvm-check-setup
sudo gvm-start
Access the web UI at https://127.0.0.1:9392, log in with generated admin credentials, navigate to Scans &gt; Tasks &gt;
New Task, and set the target IP to 192.168.56.101.
Step 6: Credential Sniffing &amp; Traffic Analysis (Wireshark)
Demonstrate the vulnerability of cleartext protocols by sniffing unencrypted FTP authentication traffic:
# Terminal 1: Launch Wireshark
sudo wireshark
# Terminal 2: Connect to FTP service on target
ftp 192.168.56.101
# User: msfadmin | Password: msfadmin
exit

In Wireshark, set display filter to &#39;ftp&#39;, right-click any captured packet, and select Follow &gt; TCP Stream to inspect
credentials transmitted in plaintext.
Step 7: SYN Flood DoS Traffic Simulation
Simulate an unmitigated TCP SYN flood attack against port 80 to observe resource exhaustion:
sudo apt install hping3 -y
sudo hping3 -S --flood -p 80 192.168.56.101
# Press Ctrl+C after ~10 seconds

Step 8: Host-Based Firewall Hardening (iptables)
Configure packet filtering rules to block insecure legacy services (Telnet/port 23) while allowing essential services
(HTTP/port 80):
# Drop inbound Telnet packets
sudo iptables -A INPUT -p tcp --dport 23 -j DROP
# Explicitly permit inbound HTTP packets
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
# Verify active filter chain rules with packet and byte counters
sudo iptables -L -v -n
