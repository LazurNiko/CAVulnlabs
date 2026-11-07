# CAVulnlabs
 
A collection of step-by-step walkthroughs (write-ups) of vulnerable virtual machines and CTF challenges, created for educational purposes. Each write-up covers the full path from reconnaissance to root access, with commands, screenshots, and explanations.
 
> 🚧 **This repository is a work in progress.** New write-ups and labs are being added gradually, so the list below will keep growing.
 
## Contents
 
| Lab | Description | Difficulty |
|-----|-------------|------------|
| [AC-cybersploit](./AC-cybersploit) | CyberSploit CTF from VulnHub: reconnaissance, working with robots.txt, Base64 decoding, SSH access, privilege escalation via a kernel exploit | Easy |
| [AC-deathnote](./AC-deathnote) | Walkthrough of the Death Note machine | - |
 
## Repository Structure
 
```
CAVulnlabs/
├── README.md
├── AC-cybersploit/
└── AC-deathnote/
└── in-progress/    
```
 
## Topics Covered
 
- Network reconnaissance and scanning (`netdiscover`, `nmap`)
- Discovering hidden web resources (`dirb`, HTML source analysis, `robots.txt`)
- Data decoding (Base64, binary)
- Gaining initial access (SSH)
- Privilege escalation (kernel exploits from Exploit-DB)
- Finding and analyzing flags
## Tools
 
Kali Linux, VirtualBox, nmap, netdiscover, dirb, Burp Suite, gcc, scp, hydra.
 
## Environment
 
All machines are run locally in **VirtualBox** on an isolated host-only network (for example, `192.168.x.x/24`). The IP addresses in the write-ups may differ depending on your network configuration.
 
## How to Use
 
1. Download the image of the machine you want (the link is provided in the corresponding write-up).
2. Run it in VirtualBox on an isolated network together with Kali Linux.
3. Follow the write-up step by step, repeating the commands yourself.
## Disclaimer
 
These materials are created **for educational purposes only**. The techniques described should only be used against your own lab machines or systems you have explicit written permission to test. The author is not responsible for any misuse of this information.
 
## Acknowledgments
 
Thanks to the authors of the virtual machines and the [VulnHub](https://www.vulnhub.com) community for providing a safe environment to practice in.
 
