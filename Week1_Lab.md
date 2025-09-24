# Week 1 – Lab Setup & Connectivity Testing  

## Lab Environment  

For the first week I focused on building the base lab environment. I spun up three VMs inside VirtualBox:  

- **Kali Linux** – my attack box and main analysis machine  
- **Ubuntu Server** – used as a target and web server  
- **Windows 10** – client machine for testing  

Networking was set up with two adapters:  
- **Host-Only (192.168.56.0/24)** – lets the VMs talk to each other and keeps the traffic isolated from my home network.  
- **NAT Network (10.0.2.0/24)** – gives internet access so I can install packages and updates.  

📸 *[Screenshot of adapter 1 setup]*
![Image alt](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/adapter%201.png)

---

## Step 1 – Checking IP Addresses  

First thing was to make sure each VM actually got an IP address on the host-only network.  

- On Kali/Ubuntu:  
```bash
ip addr show
```  
- On Windows:  
```powershell
ipconfig
```  

Each machine showed up with a `192.168.56.x` address, and Ubuntu also pulled a `10.0.2.x` from the NAT side.  

📸 *[screenshot of IP commands here]*  
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ip%20kali.png)
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ip%20ubuntu.png)
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ip%20windows.png)

---

## Step 2 – Testing Connectivity  

### Ping  
At first Kali couldn’t ping Windows (100% packet loss). Turned out Windows firewall blocks ICMP by default. After enabling the inbound Echo Request rule, pings worked both ways.  

📸 *[Screenshot of ping results]*  
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ping%20kali.png)
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ping%20ubuntu.png)
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ping%20windows.png)

### SSH into Ubuntu  
Installed OpenSSH on Ubuntu:  
```bash
sudo apt-get update
sudo apt-get install openssh-server -y
```  

From Kali I could then connect with:  
```bash
ssh <ubuntu-user>@192.168.56.102
```  

Got the password prompt which confirmed SSH was working.  

📸 *[Screenshot of SSH session]*

![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ssh%20kali%20to%20ubuntu.png)

### Apache Web Server  
Next I installed Apache on Ubuntu:  
```bash
sudo apt-get install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```  

From Windows, I browsed to:  
```
http://192.168.56.102
```  

After some tweaking (making sure Apache was listening on `*:80` and not just `127.0.0.1`), the default “It works!” page came up.  

📸 *[Screenshot of Apache default page]*
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/http%20to%20apache2.png)

---

## Step 3 – Capturing Traffic with Wireshark  

On Kali I fired up Wireshark and watched traffic flow between the machines.  

- Ping tests → filter `icmp`  
- SSH session → filter `tcp.port == 22`  
- Apache request → filter `tcp.port == 80`  

It was cool to see the raw packets from actions I triggered.  

📸 *[Screenshot of ICMP capture]* 
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/icmp%20wireshark.png)
📸 *[Screenshot of SSH capture]* 
![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/ssh%20wireshark.png)
📸 *[Screenshot of HTTP capture]*  
![Alt text]()

---

## Step 4 – Troubleshooting Notes  

A few things went wrong along the way:  

- **Kali couldn’t ping Windows** → fixed by enabling Windows firewall ICMP rule.  
- **Ubuntu couldn’t install packages** → fixed by adding a second NAT adapter and updating Netplan.  
- **Windows browser said “connection refused”** → checked with `ss -tuln | grep :80`, saw Apache listening properly, then allowed it through UFW.


📸 *[Screenshot of updating the ICMP rule in windows firewall settings]*

![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/firewall%20icmp%20allow.png)


📸 *[Screenshot of adding a second NAT adapter]*

![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/adding%20second%20NAT%20adapter.png)


📸 *[Screenshot of updating Netplan]*

![Alt text](https://github.com/arafay2/CyberSecurity/blob/63cabba394e3669cbc155c9ae36660e4b315be3a/Screenshots/Week1_screenshots/updating%20netplan.png)


📸 *[Screenshot of UFW allow rule]*

![Alt text](images/screenshot1.png)


---

## Week 1 Summary  

- Built a working 3-VM lab in VirtualBox.  
- Set up dual networking (Host-Only + NAT).  
- Verified communication with ping, SSH, and HTTP.  
- Captured all the traffic in Wireshark.  
- Learned how to troubleshoot firewall/DNS issues along the way.  

The lab is ready for Week 2 where I’ll move on to Active Directory and log forwarding into Splunk.  
