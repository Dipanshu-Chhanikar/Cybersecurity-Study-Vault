# IP Addressing & Subnetting: The Map of the Network

### Overview
Every device on a network needs an address. As a Security Professional, you need to look at an IP address and instantly know:
1.  **Is it Internal or External?** (Private vs. Public).
2.  **How big is the network?** (CIDR Notation).
3.  **Is it a specific host or a whole subnet?** (/32 vs. /24).

---

### **1. The Structure (IPv4)**
An IPv4 address is 32 bits long, divided into 4 octets (e.g., `192.168.1.5`).

#### **CIDR Notation (The Slash `/` Number)**
Classless Inter-Domain Routing (CIDR) tells you the size of the network (Subnet Mask).

| CIDR | Subnet Mask | Total IPs | Usage Scenario |
| :--- | :--- | :--- | :--- |
| **/32** | `255.255.255.255` | **1** | A single specific Host. Used for precise firewall rules. |
| **/30** | `255.255.255.252` | **4** (2 Usable) | Point-to-Point links (Router to Router). |
| **/24** | `255.255.255.0` | **256** (254 Usable)| Standard LAN / Office Network. |
| **/16** | `255.255.0.0` | **65,536** | Large Campus / Enterprise Network. |
| **/8** | `255.0.0.0` | **16 Million+** | Massive ISPs or Loopback (`127.0.0.0/8`). |

**Security Tip:**
* Block `/32` to stop **one attacker**.
* Block `/24` to stop **an entire neighborhood** (subnet).

---

### **2. Private vs. Public IPs (RFC 1918)**
Not all IPs can go on the internet. **Private IPs** are reserved for internal networks (LANs). They are **not routable** over the internet.

**You MUST memorize these ranges:**

| Class | Range Start | Range End | Typical Use |
| :--- | :--- | :--- | :--- |
| **Class A** | `10.0.0.0` | `10.255.255.255` | Large Enterprise Networks. |
| **Class B** | `172.16.0.0` | `172.31.255.255` | Cloud (AWS/Azure) & Corporate. |
| **Class C** | `192.168.0.0` | `192.168.255.255` | Home Routers & Small Offices. |

**The Golden Rule:**
If you see an IP like `10.5.5.5` in your firewall logs attacking you, the call is coming from **inside the house** (Insider Threat or Compromised Host).

---

### **3. NAT (Network Address Translation)**
Since Private IPs cannot go on the internet, we use NAT.
* **The Function:** Your Router replaces your "Private Internal IP" with its own "Public External IP" before sending data to Google/Facebook.
* **Security Benefit:** It acts as a basic firewall. Hackers on the internet cannot directly ping your laptop (`192.168.1.50`) because that IP doesn't exist on the public web.

---

### **4. IPv6 (Brief Overview)**
* **Why:** We ran out of IPv4 addresses.
* **Structure:** 128-bit address, written in Hexadecimal (e.g., `2001:0db8:...`).
* **Key Difference:** No Broadcasts (uses Multicast), and generally **No NAT** is needed (every device gets a real public IP).

---

### **Interview Q&A Practice**

**Q1: You see an alert for a "Brute Force Attack" coming from `10.10.5.50`. Is this external or internal?**  
**A:** **Internal.** The `10.x.x.x` range is a Private IP (Class A). This means the attack is coming from a device already inside our network (e.g., a compromised laptop or a malicious employee).

**Q2: What is "Lateral Movement" and how does Subnetting prevent it?**  
**A:** **Lateral Movement** is when an attacker compromises one machine and moves to others. If a company has a "Flat Network" (e.g., a giant `/16`), the attacker can reach everything.
**Defense:** We use **Network Segmentation** (breaking the network into smaller `/24` VLANs) and put firewalls between them to stop the attacker from jumping across.

**Q3: You need to block a malicious server at `203.0.113.5`. You accidentally type `203.0.113.5/24` in the firewall. What happens?**  
**A:** You have blocked the entire subnet (256 IPs). You likely blocked not just the attacker, but also innocent users or legitimate servers hosted by the same provider in that range. You should have used `/32`.

**Q4: What is the Loopback Address?**  
**A:** `127.0.0.1` (or `::1` in IPv6). It is used to test the network stack on the local machine ("localhost").

**Q5: A developer asks you to open a firewall port for a "Public" server with the IP** `172.16.50.10`. **What is your response?**  
**A:** **Deny/Correct.** The `172.16.x.x` range is part of the RFC 1918 Private Class B range. This server is not globally routable on the public internet. If they want it to be public, they need a "Public IP" from an ISP or must configure a **Static NAT (1-to-1 NAT)** on the edge firewall.

**Q6: What is the "First Usable" and "Last Usable" IP in the subnet 192.168.1.0/24?**  
**A:** **First Usable:** `192.168.1.1` (usually assigned to the Default Gateway/Router).  
* **Last Usable:** `192.168.1.254.`  
* **Note:** `192.168.1.0` is the **Network ID** and `192.168.1.255` is the **Broadcast Address.** Neither can be assigned to a laptop or server.

**Q7: You see a device with an IP address of** `169.254.10.55.` **What does this tell you about the network’s health?**  
**A:** This is an **APIPA (Automatic Private IP Addressing)** address. It means the device tried to get an IP from a **DHCP Server** but failed. From a security perspective, this device is "marooned"—it can only talk to other devices on the same physical wire that also failed to get an IP. It cannot reach the gateway or the internet.

**Q8: Explain the security risk of a "Flat Network" (e.g., a single** `/16` **for the whole company).**  
**A:** In a flat network, there is no **internal segmentation.** If a guest's malware-infected phone connects to the Wi-Fi, it can "see" and attempt to exploit the HR servers, the Point-of-Sale (POS) system, and the Domain Controller because they are all on the same broadcast domain.

**Q9: In an IPv6 environment, what is a "Link-Local" address, and how do you recognize it?**  
**A:** A Link-Local address (starting with `fe80::`) is used for communication within a single local network segment. Unlike IPv4, every IPv6 interface automatically assigns itself one of these, even if no global IP is present.

**Q10: Why would a Security Analyst use a** `/30` **or** `/31` **subnet for a "DMZ" link between two firewalls?**  
**A:** To minimize the **Attack Surface.** A `/30` only provides 2 usable IP addresses. If an attacker compromises one device, there are no "extra" IP addresses in that subnet for them to scan or hide other rogue devices on.

---

### **Bonus: The "Cheat Sheet" Table for Subnetting Math**

To quickly calculate how many hosts are in a subnet, use the following logic where $n$ represents the number of **host bits** (calculated as $32 - \text{CIDR}$):

* **Total IP Addresses:** $2^n$
* **Usable IP Addresses:** $2^n - 2$ *(Subtracting the Network ID and the Broadcast Address)*



| CIDR | Full Calculation ($32 - \text{CIDR}$) | Total IPs | Usable IPs | Common Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **/32** | $2^{(32-32)} = 2^0$ | 1 | 1 | Single Host (Firewall rule) |
| **/31** | $2^{(32-31)} = 2^1$ | 2 | 2* | Point-to-Point (RFC 3021) |
| **/30** | $2^{(32-30)} = 2^2$ | 4 | 2 | Point-to-Point (Router link) |
| **/29** | $2^{(32-29)} = 2^3$ | 8 | 6 | Small Public IP block |
| **/28** | $2^{(32-28)} = 2^4$ | 16 | 14 | Small DMZ or Subnet |
| **/27** | $2^{(32-27)} = 2^5$ | 32 | 30 | Small Office Department |
| **/26** | $2^{(32-26)} = 2^6$ | 64 | 62 | Medium Subnet |
| **/25** | $2^{(32-25)} = 2^7$ | 128 | 126 | Large Subnet |
| **/24** | $2^{(32-24)} = 2^8$ | 256 | 254 | Standard LAN |

> **Security Note:** Always remember to subtract **2** for usable IPs (one for the Network ID and one for the Broadcast), unless you are dealing with a /32 or the special /31 point-to-point exception.
