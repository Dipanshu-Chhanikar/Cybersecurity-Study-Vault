# Core Protocols: The "Big 5" (DNS, DHCP, HTTP, FTP, SSH)

### Overview
In Cybersecurity, we don't just use protocols; we analyze them for abuse.
* **Admins** ask: "Is DNS working?"
* **Security Analysts** ask: "Is DNS being used to steal data?"

---

### **1. DNS (Domain Name System) - Port 53**
* **Function:** Translates human-readable names (`google.com`) into IP addresses (`142.250.x.x`).
* **Protocol:** Uses **UDP** (for speed) but can use **TCP** (for large transfers like Zone Transfers).

#### **The Resolution Process (Recursion)**
1.  **Client:** Checks local cache / hosts file.
2.  **Resolver (ISP/8.8.8.8):** "I don't know, let me ask the Root."
3.  **Root Server (.):** "Go to the `.com` server."
4.  **TLD Server (.com):** "Go to Google's Authoritative Server."
5.  **Authoritative Server:** "Here is the IP."

#### **Security Twist: DNS Tunneling**
* **The Attack:** Firewalls often leave Port 53 open. Attackers encode stolen data into the "subdomain" part of a query.
* **Example:** `password-file-chunk1.attacker.com`
* **Detection:** Look for high volumes of DNS queries to a single random domain with long, gibberish subdomains.

---

### **2. DHCP (Dynamic Host Configuration Protocol) - UDP 67/68**
* **Function:** Automatically assigns IP addresses, Subnet Masks, and Default Gateways to devices when they join a network.

#### **The DORA Process**
1.  **D**iscover: Client broadcasts "I need an IP!" (Source: `0.0.0.0`, Dest: `255.255.255.255`).
2.  **O**ffer: DHCP Server says "Here is `192.168.1.50`."
3.  **R**equest: Client says "I'll take it."
4.  **A**cknowledge: Server says "Confirmed. It's yours for 24 hours."

#### **Security Twist: Rogue DHCP (Spoofing)**
* **The Attack:** An attacker plugs in a router/server that responds faster than the real DHCP server.
* **The Goal:** The attacker becomes the **Default Gateway** for the victim, intercepting all their traffic (Man-in-the-Middle).

---

### **3. HTTP vs. HTTPS (Web Traffic) - TCP 80 vs 443**
* **HTTP (80):** **Cleartext.** Everything (including passwords) is visible to anyone on the wire (Wireshark).
* **HTTPS (443):** **Encrypted.** Uses TLS (Transport Layer Security) to encrypt the session.
    * **Certificate:** Verifies the identity of the server (prevents you from logging into a fake bank site).

---

### **4. FTP (File Transfer Protocol) - TCP 20/21**
* **Status:** **OBSOLETE / DANGEROUS.**
* **Risk:** Sends credentials (username/password) in **Cleartext**.
* **Secure Alternative:** **SFTP** (runs over SSH) or **FTPS** (runs over SSL).

---

### **5. SSH (Secure Shell) - TCP 22**
* **Function:** Secure, encrypted replacement for Telnet. Used for remote administration of Linux servers.
* **Authentication:**
    1.  **Password:** (Weak, bruteforceable).
    2.  **Key-Based:** (Strong). You place your **Public Key** on the server (`authorized_keys` file) and keep your **Private Key** on your machine.

---

### **Interview Q&A Practice**

**Q1: Explain the "DORA" process in DHCP.**
* **A:** It stands for **Discover, Offer, Request, Acknowledge.** It is the 4-step handshake a client uses to get an IP address from a DHCP server.

**Q2: You see a laptop making thousands of DNS queries to subdomains like `a1b2.hacker.com`. What is this?**
* **A:** **DNS Tunneling (Data Exfiltration).** The attacker is likely encoding stolen data into the subdomains to sneak it past the firewall, which assumes DNS traffic is safe.

**Q3: Why is FTP considered a "Critical" vulnerability in a Pen Test report?**
* **A:** Because it transmits credentials in **Cleartext.** An attacker on the same network can sniff the traffic and capture the admin's password instantly.

**Q4: How do you prevent a Rogue DHCP Server attack?**
* **A:** Enable **DHCP Snooping** on the network switches. This feature ensures that DHCP "Offer" packets are only allowed from trusted ports (the real server) and blocks them from untrusted user ports.

**Q5: What is the difference between encryption and hashing?**
* **A:**
    * **Encryption (Two-Way):** Scrambles data so it can be unscrambled later with a key (e.g., HTTPS, SSH).
    * **Hashing (One-Way):** Scrambles data into a fixed string (fingerprint) that *cannot* be reversed (e.g., storing passwords).
