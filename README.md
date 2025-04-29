# Penetration Testing Assignment - Linux Distributions and HTB Academy

Reviewed: No

# Linux Security Distributions & Penetration Testing

Assignment for Implementation of UNIX-LINUX course focusing on security-oriented Linux distributions and practical penetration testing.

## Table of Contents

- [Introduction](#introduction)
- [Linux Security Distributions](#linux-distributions)
    - Kali Linux
    - Parrot OS
- [Penetration Testing Fundamentals](#pentesting)
- [Practical Implementation - HTB Nibbles](#practical)

## Introduction

This repository contains detailed analysis and practical implementation of penetration testing using specialized Linux distributions. The focus is on understanding the key differences between security-focused distributions and their practical applications in cybersecurity.

## Linux Security Distributions

### Kali Linux

Kali Linux is a Debian-derived Linux distribution designed for digital forensics and penetration testing.

- **Key Features:**
    - 600+ pre-installed penetration testing tools
    - Open-source and free to use
    - Regular security updates
    - Multi-language support
    - Customizable kernel

[Insert Image: Kali Linux Desktop Environment]

- **Core Tools:**
    - Metasploit Framework
    - Wireshark
    - John the Ripper
    - Aircrack-ng
    - Burp Suite

### Parrot OS

Parrot Security OS is a Debian-based Linux distribution focused on security, development, and privacy.

- **Key Features:**
    - Lightweight system requirements
    - Development environment tools
    - Cryptography tools
    - Cloud penetration testing tools
    - Anonymous mode

[Insert Image: Parrot OS Desktop Environment]

### Comparison Table

| Feature | Kali Linux | Parrot OS |
| --- | --- | --- |
| Base | Debian | Debian |
| RAM Requirements | 2GB minimum | 1GB minimum |
| Default Desktop | Xfce | MATE |
| Target Audience | Security Professionals | Security & Development |
| Tool Count | 600+ | 400+ |

Both distributions serve similar purposes but have distinct characteristics that make them suitable for different use cases. Kali Linux is more focused on penetration testing, while Parrot OS provides a broader range of development tools and emphasizes system privacy.

# Penetration Testing Fundamentals

Penetration testing, also known as ethical hacking, is a systematic process of evaluating system security by simulating real-world attacks. This section covers the essential components and methodologies of penetration testing.

## Core Components of Penetration Testing

- **Information Gathering:**
    - Network and domain information collection
    - Service enumeration
    - Asset identification
- **Vulnerability Assessment:**
    - System scanning
    - Service version analysis
    - Security weakness identification
- **Exploitation Phase:**
    - Vulnerability verification
    - Initial access establishment
    - Privilege escalation attempts

## Essential Tools and Techniques

- **Reconnaissance Tools:**
    - Nmap - Network mapping
    - Gobuster - Directory enumeration
    - Whois - Domain information
- **Exploitation Tools:**
    - Metasploit Framework
    - Burp Suite
    - SQLmap

## Best Practices

- Document all findings and steps
- Follow ethical guidelines
- Maintain clear communication with stakeholders
- Use proper scope definition
- Create detailed reports

### Methodology Diagram

![image](https://github.com/user-attachments/assets/83a44aeb-66d7-4fd0-a1d6-b9c3d6e2e859)

The penetration testing lifecycle consists of 5 key phases:

1. Pre-Engagement - Planning the scope and approach with the client
2. Reconnaissance/Information Gathering - Gathering crucial information about the target systems
3. Vulnerability Assessment - Identifying and analyzing security weaknesses
4. Exploitation - Gaining access and exploring system vulnerabilities
5. Documentation - Creating detailed reports and recommendations

These phases are not strictly linear - testers often cycle back through earlier phases as they discover new information or potential attack vectors.

The penetration testing process is iterative and may require multiple cycles through these phases to achieve comprehensive security assessment.

# Practical Example - HTB Cap

## Penetration Testing Steps

Let's break down the penetration testing process into clear steps:

1. Network Scanning - Using tools to discover what services and ports are running on target systems
2. Enumeration - Detailed exploration of each discovered service to understand potential vulnerabilities
3. Initial Access - Exploiting found vulnerabilities to access the system
4. Privilege Escalation - Moving from basic user access to administrator/root level permissions

## Step 1: Initial Network Scan

We start by running a basic network scan:

```bash
└─$ nmap -sC -sV 10.10.10.245
```

## Step 2: Service Enumeration

We found a Security Snapshot feature with URLs following this pattern:

`/[something]/[id]` where:

- The `[id]` represents the scan number
- We need to discover what goes in `[something]`

We discovered a security weakness: changing the ID in the URL lets us view other users' scan results.

![](image%201.png)

Our next step is methodically examining each packet capture file for useful information.

During our analysis, we found login credentials being sent through FTP (which isn't secure as it sends passwords as plain text).

Since we found SSH running on port 22 earlier, we can try these FTP credentials to log in via SSH.

### FTP

Let's check the FTP service.

First, we try anonymous FTP access, but it's disabled. Moving on to the HTTP server.

### HTTP

The HTTP server runs Gunicorn (a Python-based server) on port 80. When we visit the site, we find a dashboard with several features:

- IP Config page - shows ifconfig output
- Network Status page - displays netstat output
- Security Snapshot - captures network traffic

![image.png](image%202.png)

When we download a Security Snapshot, we get a packet capture file viewable in Wireshark. Our own captured traffic shows nothing interesting.

![image.png](image%203.png)

However, we notice the URL pattern `/data/[id]` for captures. By testing `/data/0`, we find an older capture file - this is called an Insecure Direct Object Reference (IDOR) vulnerability.

![image.png](image%204.png)

Examining this capture in Wireshark reveals unencrypted FTP login credentials: username 'nathan' with password 'Buck3tH4TF0RM3!'. 

These credentials work for both FTP and SSH access.

# Getting System Access

Now we can try log into the system using SSH:

```bash
ssh nathan@IP
```

To view the user flag, enter: `cat user.txt`

# Gaining Administrator Rights

Follow these steps to get full system access:

1. Download LinPEAS (a tool that helps find security weaknesses) from [https://github.com/Cyberw1ng/Bug-Bounty](https://github.com/Cyberw1ng/Bug-Bounty)
2. Create a file called [linpeas.sh](http://linpeas.sh) using `nano linpeas.sh`, copy the LinPEAS code into it, and save
3. Make the file runnable with `chmod +x linpeas.sh` and run it using `./linpeas.sh`
    
    ![image.png](image%205.png)
    
4. The scan shows we can use Python3's special permissions to gain administrator access

Here's how to get administrator (root) access using Python3:

1. Start Python3 by typing `/usr/bin/python3.0`
2. Enter these commands:

```bash
import os
os.setuid(0)
os.system('/bin/sh')
```

![image.png](image%206.png)

What these commands do:

- Change our user ID to 0 (root/administrator)
- Open a new command prompt with full system access[Uploading Penetration Testing Assignment - Linux Distributio 1b4de124bd03808bbec6f865faf5e859.md…]()

![image 6](https://github.com/user-attachments/assets/379cf231-1d34-4bff-845b-585349d995a9)
![image 5](https://github.com/user-attachments/assets/edeb49e4-d45c-408a-9aa0-bc500747899f)
![image 4](https://github.com/user-attachments/assets/69d87657-5d10-4685-bf11-4ad3333cf273)
![image 3](https://github.com/user-attachments/assets/d9b61648-39ac-4f31-97e7-c81430b84f22)
![image 2](https://github.com/user-attachments/assets/9c1151e2-688f-4eb3-b8b7-0d2746f03ca3)
![image 1](https://github.com/user-attachments/assets/5d52c4bb-ad7e-4ad6-ae7e-3d389649bb93)
