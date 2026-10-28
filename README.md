Step 1 — Passive recon

bash
whois google.com
bash
nslookup google.com

Step 2 — Active recon / ping sweep

bash
nmap -sn 192.168.56.0/24

Step 3 — Port & service scanning (target: Metasploitable2)

bash
nmap -sS -p- 192.168.56.101 -oN tcp_scan.txt
bash
sudo nmap -sU --top-ports 100 192.168.56.101 -oN udp_scan.txt
bash
nmap -sV 192.168.56.101 -oN service_scan.txt
bash
sudo nmap -O 192.168.56.101 -oN os_scan.txt
bash
sudo nmap -sV -O -p- 192.168.56.101 -oA full_scan_report

Step 4 — View your scan results

bash
cat full_scan_report.nmap

Step 5 — Install and start OpenVAS/GVM

bash
sudo apt install gvm -y
bash
sudo gvm-setup
bash
sudo gvm-check-setup
bash
sudo gvm-start

Open Firefox in Kali:

https://127.0.0.1:9392

Login with the admin password printed at the end of gvm-setup → Scans → Tasks → New Task → target 192.168.56.101

Step 6 — Wireshark FTP credential capture

bash
sudo wireshark

Start capture on your host-only interface. In a second terminal:

bash
ftp 192.168.56.101

Login with:

msfadmin
msfadmin
bash
exit

In Wireshark: type filter ftp and press Enter → right-click a packet → Follow → TCP Stream to see the plaintext login.

Step 7 — SYN flood simulation

bash
sudo apt install hping3 -y
bash
sudo hping3 -S --flood -p 80 192.168.56.101

Press Ctrl+C to stop after ~10 seconds.

Step 8 — Firewall rules (iptables)

bash
sudo iptables -A INPUT -p tcp --dport 23 -j DROP
bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
bash
sudo iptables -L -v -n
