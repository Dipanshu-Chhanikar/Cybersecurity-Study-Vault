# OSI Model: The 7-Story Building of Networking

### Overview
The Open Systems Interconnection (OSI) model is a conceptual framework used to understand how data moves across a network. In Cybersecurity, we use this to identify where attacks occur and where to place our defenses.

---

### **The 7 Layers Breakdown**

* **Layer 7 - Application:** The layer where the user interacts with the network. It provides network services to applications (Web browsers, Email).
* **Layer 6 - Presentation:** Translates data into a format the application layer can understand. Handles **Encryption**, compression, and translation.
* **Layer 5 - Session:** Manages the connection between two nodes. It starts, manages, and terminates "sessions" or conversations.
* **Layer 4 - Transport:** Handles end-to-end communication and error recovery. Uses **TCP** (reliable) and **UDP** (fast).
* **Layer 3 - Network:** Responsible for logical addressing (IP addresses) and routing data packets between different networks.
* **Layer 2 - Data Link:** Handles physical addressing (**MAC addresses**) and error detection from the physical layer.
* **Layer 1 - Physical:** The actual hardware—cables, radio waves, and electrical pulses that transmit raw bits.

---

### **Quick Reference Table**



| Layer | Name | Data Unit (PDU) | Key Device/Protocols | Security Perspective / Common Attacks |
| :--- | :--- | :--- | :--- | :--- |
| **7** | **Application** | Data | HTTP, DNS, FTP, SMTP | **SQL Injection, Phishing, XSS** |
| **6** | **Presentation**| Data | SSL/TLS, JPEG, ASCII | **Encryption/Decryption** |
| **5** | **Session** | Data | NetBIOS, RPC, Sockets | **Session Hijacking** |
| **4** | **Transport** | **Segment** | TCP, UDP (Firewalls) | **SYN Flooding, Port Scanning** |
| **3** | **Network** | **Packet** | Routers, IP, ICMP | **IP Spoofing, DDoS** |
| **2** | **Data Link** | **Frame** | Switches, MAC, ARP | **ARP Spoofing, MAC Flooding** |
| **1** | **Physical** | **Bits** | Hubs, Cables, NICs | **Wiretapping, Physical Tampering** |

---

### **Core Concepts**



* **Encapsulation:** As data moves from Layer 7 down to Layer 1, each layer adds its own "header" (info). Think of it like putting a letter inside 7 different envelopes.
* **De-encapsulation:** When the receiver gets the data, they strip the headers back off as it moves from Layer 1 up to Layer 7.

---

### **Interview Q&A Practice**

**Q1: Where do Firewalls live?**  
**A:** A standard firewall works at **Layer 4 (Transport)** by filtering Ports. However, a Web Application Firewall (WAF) works at **Layer 7 (Application)** to inspect data for attacks like SQLi.

**Q2: If a LAN cable is physically damaged, which layer is affected?**  
**A:** **Layer 1 (Physical Layer)**. This layer deals with the hardware and electrical signals.

**Q3: A user can ping an IP but cannot open a website (DNS issue). Where is the problem?**  
**A:** **Layer 7 (Application Layer)**. DNS is a protocol used at the application layer to translate names into IPs.

**Q4: Which layer is responsible for encrypting data (like SSL/TLS)?**  
**A:** **Layer 6 (Presentation Layer)**. It handles formatting, compression, and encryption.

**Q5: If you see a MAC Address problem, which layer are you investigating?**  
**A:** **Layer 2 (Data Link Layer)**. Switches and MAC addresses operate here.

**Q6: At which layer does a Router operate and why?**  
**A:** **Layer 3 (Network Layer)**. Routers use IP addresses to find the best path for data.

**Q7: What is the Data Unit (PDU) name for Layer 4?**  
**A:** It is called a **Segment**. (Reminder: Layer 3 = Packet, Layer 2 = Frame).

**Q8: Why does a WAF work at Layer 7?**  
**A:** It needs to "read" the full web request to find malicious code like XSS or SQL Injection that lower layers cannot see.

---

### **Mnemonics to Remember**
* **Top to Bottom (7 to 1):** **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.
* **Bottom to Top (1 to 7):** **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way.
